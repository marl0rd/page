# C++ - GDB
___

GDB breakpoints, workflow, and C++ debugging cheat sheet.

$~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~$

## Common Breakpoints
- `b Model::start`
- `set startRequested_ = true`

## Screen Refresh
- Refresh screen after Ctrl+A: `Ctrl+L` | Redraws the current screen

## Threads
- See all bt threads `thread apply all bt` or `t a a bt`  
- Source: `https://stackoverflow.com/questions/18391808/how-do-i-get-the-backtrace-for-all-the-threads-in-gdb`  

## DispatcherWorkers
- ✔️ `disp this->dispatcher_.pImpl_.get().workers_`  

## Breakpoints
- Set breakpoint by function name: `b <function_name>` | e.g.: `b main`, `b MyClass::processData`
- Set breakpoint by file and line: `b <file.cpp>:<line_number>` | e.g.: `b main.cpp:25`, `b src/util.cpp:100`
- Set breakpoint by line in current file: `b <line_number>` | e.g.: `b 42`
- Set conditional breakpoint: `b <location> if <condition>` | e.g.: `b main.cpp:30 if i == 5`, `b pd if this->isV()`
- List all breakpoints: `info b`
- Delete breakpoint: `d <breakpoint_number>` | e.g.: `d 1`, `d 3`
- Enable breakpoint: `enable <breakpoint_number>` | e.g.: `enable 2`
- Disable breakpoint: `disable <breakpoint_number>` | e.g.: `disable 1`
- Show current location: `where` or `frame` | e.g.: `where` (shows current frame with file and line)
- List source code at current location: `list` | e.g.: `list` (shows code around current line)
- Show current file and line: `frame` or `f` | e.g.: `frame` (shows current stack frame with location)
- Show current function: `info frame` | e.g.: `info frame` (detailed info about current frame)
- Print current line number: `p $pc` | e.g.: `p $pc` (shows program counter address)

## Variable Inspection & Monitoring
- Print value of variable: `p <variable_name>` | e.g.: `p my_var`, `p *ptr_to_obj`, `p object.member`
- Print value with format: `p/<format> <var_name>` | e.g.: `p/x my_int` (hex), `p/t my_byte` (bin), `p/c my_char`
- Print value and type: `ptype <variable_name>` | e.g.: `ptype my_object`, `ptype arr`
- Undisplay variable: `undisplay <display_number>` | e.g.: `undisplay 1` (use `info display` to see numbers)
- Print arguments of current frame: `info args`
- Print local variables of current frame: `info locals`
- Print information about the "this" pointer: `p *this` | e.g.: `p *this` (shows all members), `p this` (shows address)



## Stack & Frame Inspection
- Backtrace (call stack): `bt` (or `backtrace`)
- Select stack frame: `frame <frame_number>` | e.g.: `frame 0` (current), `frame 1` (caller)