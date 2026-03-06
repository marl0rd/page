# MW - notes about Boost.Asio thread_pool — overview and thread naming
___

## Concepts
- thread_pool
  - - Fixed-size worker threads consuming a shared task queue.
  - - Executor: pool.get_executor() used to post/dispatch tasks.
  - - Strands: boost::asio::strand<E> for handler serialization on same executor.
  - - Posting:
    - ✔️ post: enqueue and return; runs later.
    - ✔️ dispatch: run inline if calling thread belongs to the pool; else enqueue.
    - ✔️ defer: like post but hints for continuation.
- Lifetime
  - - Work tracking prevents premature exit:
    - ✔️ use_executor_guard: boost::asio::executor_work_guard<executor_type>.
    - ✔️ create/destroy guards to control pool lifetime.
- Handler rules
  - - Non-blocking, short, exception-safe.
  - - Use strands for shared state.
  - - Prefer move-only handlers ok.
  - ✔️ Example safe pattern; ❌ Blocking/waiting in handlers.
- Performance
  - - One lock-protected queue; N workers.
  - - Use strands sparingly; over-serialization hurts.
  - - Avoid long handlers; yield back quickly.

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Basic usage
- ✔️ Create pool and post
  ```c++
    boost::asio::thread_pool pool(std::max(2u, std::thread::hardware_concurrency()-1u));
    boost::asio::post(pool, []{ /* work */ });
    pool.join(); // or allow RAII
  ```
- ✔️ Get executor and make strand
  ```c++
    auto ex = pool.get_executor();
    boost::asio::strand<decltype(ex)> st{ex};
    boost::asio::post(st, []{ /* serialized */ });
  ```

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Thread identification (naming) — simplest practical options
- Goal: mark “this thread is from the pool” for diagnostics.
- Option A: set OS-level thread name in each worker thread (recommended)
  - - Hook into pool threads at creation time is not exposed directly; use a startup task per thread that sets name once.
  - - Pattern: schedule N one-time init tasks guarded by a thread_local flag.
  - ✔️ Works cross-component; ❌ OS API differences.
  ```c++
  // Cross-platform helpers (POSIX/Windows) — minimal
  inline void set_current_thread_name(const char* name) {
  #if defined(_WIN32)
    // Windows 10 1607+: SetThreadDescription (UTF-16)
    using SetThreadDescription_t = HRESULT(WINAPI*)(HANDLE, PCWSTR);
    static auto p = reinterpret_cast	SetThreadDescription_t	(
      GetProcAddress(GetModuleHandleW(L"Kernel32.dll"), "SetThreadDescription"));
    if (p) {
      int wlen = MultiByteToWideChar(CP_UTF8, 0, name, -1, nullptr, 0);
      std::wstring wname(wlen, L'\0');
      MultiByteToWideChar(CP_UTF8, 0, name, -1, wname.data(), wlen);
      p(GetCurrentThread(), wname.c_str());
    }
  #elif defined(__APPLE__)
    pthread_setname_np(name);
  #elif defined(__linux__)
    pthread_setname_np(pthread_self(), name); // 16-char limit
  #else
    (void)name;
  #endif
  }

  // One-time initializer task
  inline void mark_pool_thread_once(const char* base) {
    thread_local bool named = false;
    if (!named) {
      set_current_thread_name(base);
      named = true;
    }
  }

  // Submit one init task per thread (best-effort)
  void name_pool_threads(boost::asio::thread_pool& pool, unsigned count, const std::string& base) {
    for (unsigned i = 0; i < count; ++i) {
      boost::asio::post(pool, [base]{
        mark_pool_thread_once(base.c_str());
      });
    }
  }

  // Usage (after constructing pool_)
  auto n = std::max(2u, std::thread::hardware_concurrency()-1u);
  boost::asio::thread_pool pool(n);
  name_pool_threads(pool, n, "slrt-pool");
  ```
- Option B: thread-local marker + logging prefix (no OS name)
  - - Set a thread_local flag in pool threads via the same one-time init task; check it in logs.
  - ✔️ Easiest to integrate; ❌ doesn’t show in external debuggers.
  ```c++
  thread_local bool g_is_pool_thread = false;

  inline void mark_pool_thread_flag() {
    if (!g_is_pool_thread) g_is_pool_thread = true;
  }

  void identify_pool_threads(boost::asio::thread_pool& pool, unsigned count) {
    for (unsigned i = 0; i < count; ++i) {
      boost::asio::post(pool, []{ mark_pool_thread_flag(); });
    }
  }

  // In logger: if (g_is_pool_thread) add "[pool]" tag
  ```
- Option C: wrap executor with a decorating executor to run a pre-handler
  - - Use boost::asio::bind_executor or custom executor that sets name/flag before every handler.
  - ✔️ Robust; ❌ adds wrapper code.
  ```c++
  auto ex = pool.get_executor();
  auto decorated = boost::asio::bind_executor(ex, [/*cap*/](auto&& fn){
    return [f=std::forward<decltype(fn)>(fn)]{
      // set flag or thread name lazily (thread_local guard)
      mark_pool_thread_once("slrt-pool");
      f();
    };
  });
  // Post via decorated executor
  boost::asio::post(decorated, []{ /* work */ });
  ```

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Minimal change for your codebase
- ✔️ Add thread naming/flag after pool_ construction inside Impl()
  ```c++
  struct SLRTDispatcher::Impl {
    boost::asio::thread_pool pool_;
    // ...
    Impl()
    : pool_(std::max(2u, std::thread::hardware_concurrency() - 1u))
    , /* ... */
    {
      const auto n = std::max(2u, std::thread::hardware_concurrency() - 1u);
      name_pool_threads(pool_, n, "slrt-pool"); // Option A
      // Or: identify_pool_threads(pool_, n);     // Option B
    }
  };
  ```
- ✔️ Optional: decorate executors so any future thread picks up the marker
  ```c++
  auto ex = pImpl_->pool_.get_executor();
  auto decorate = [=](auto&& f){
    return [g=std::forward<decltype(f)>(f)]{
      mark_pool_thread_once("slrt-pool");
      g();
    };
  };
  auto exec_with_mark = boost::asio::prefer(ex, boost::asio::execution::relationship.fork); // optional
  // When assigning to workers:
  worker->executor_ = std::make_unique<executor_type>(ex);
  // Wrap posts: boost::asio::post(ex, decorate(handler));
  ```

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## ✔️/❌ examples
- ✔️ Name pool threads once
  ```c++
  name_pool_threads(pool, n, "slrt-pool");
  boost::asio::post(pool, []{ /* logs show [slrt-pool] */ });
  ```
- ❌ Attempt to rename from non-pool thread without running inside the pool
  ```c++
  set_current_thread_name("slrt-pool"); // ❌ names the caller thread, not pool worker
  ```
- ✔️ Identify via thread_local flag
  ```c++
  identify_pool_threads(pool, n);
  boost::asio::post(pool, []{
    if (g_is_pool_thread) LOG(debug,0) << "[pool] running";
  });
  ```
- ❌ Assuming pool exposes thread handles
  ```c++
    // pool.threads() // ❌ not available; thread_pool hides worker threads
  ```

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Notes and caveats
- - OS name limits:
  - - Linux: 16 bytes incl. null.
  - - macOS: 64 bytes typical.
  - - Windows: arbitrary length via SetThreadDescription (UTF-16).
- - Run a few warm-up posts early to ensure each worker processes at least one init task.
- - If a worker never runs the init task, its name/flag won’t be set; decorating handlers guarantees coverage.

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## References
- - Boost.Asio thread_pool: https://www.boost.org/doc/libs/release/doc/html/boost_asio/reference/thread_pool.html
- - Executors and strands: https://www.boost.org/doc/libs/release/doc/html/boost_asio/reference.html
- - POSIX pthread_setname_np: Linux/man: https://man7.org/linux/man-pages/man3/pthread_setname_np.3.html
- - Windows SetThreadDescription: https://learn.microsoft.com/windows/win32/api/processthreadsapi/nf-processthreadsapi-setthreaddescription
- - Asio execution functions: https://www.boost.org/doc/libs/release/doc/html/boost_asio/reference/post.html
