# C++ - GTEST

## installation
## Google's Recommendation
-   Google recommends project-local building.
    - CMake’s FetchContent https://google.github.io/googletest/quickstart-cmake.html
- Example:
```bash
  # GoogleTest requires at least C++17
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)

  include(FetchContent)
  FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/03597a01ee50ed33e9dfd640b249b4be3799d395.zip
  )
```
- Why?
  - GoogleTest built with same `compiler` and `flags` as project code.
  - Different projects can use different GoogleTest versions.
  - Avoids `global namespace pollution`.
  - Prevents `version clashes`.
  - Protects against `accidental upgrades/removals`.

## SLRT Target way:
- install using sudo apt install googletest
- check with sudo apt-cache search googletest
- Compile your code with the following CMakeLists:

```bash
  cmake_minimum_required(VERSION 3.10)
  
  project(C++ CXX)
  
  include(FetchContent)
  FetchContent_Declare(
    googletest
    SOURCE_DIR /usr/src/googletest
  )
  FetchContent_MakeAvailable(googletest)
  
  enable_testing()
  add_executable(hellotest src/gtest/hello.cpp)
  target_link_libraries(hellotest GTest::gtest_main)
  include(GoogleTest)
  gtest_discover_tests(hellotest)
```

## Cheat Sheet
## Basic Assertions
- Equality: `EXPECT_EQ(expected, actual);` | e.g.: `EXPECT_EQ(5, sum(2, 3));`
- Inequality: `EXPECT_NE(val1, val2);` | e.g.: `EXPECT_NE(0, result);`
- True/False: `EXPECT_TRUE(condition);` | e.g.: `EXPECT_TRUE(isValid);`
- True/False: `EXPECT_FALSE(condition);` | e.g.: `EXPECT_FALSE(isError);`
- Null Pointer: `EXPECT_NE(nullptr, ptr);` | e.g.: `EXPECT_NE(nullptr, myObject.get());`
- Null Pointer: `EXPECT_EQ(nullptr, ptr);` | e.g.: `EXPECT_EQ(nullptr, myObject.get());`
- Greater Than: `EXPECT_GT(val1, val2);` | e.g.: `EXPECT_GT(10, 5);`
- Less Than: `EXPECT_LT(val1, val2);` | e.g.: `EXPECT_LT(5, 10);`

## Empty assertions:
- SUCCEED()
- FAIL(): marks the test as failed, outputs a message, and stops execution.
- ADD_FAILURE(): marks the test as failed, outputs a message, but allows execution to continue similar to EXPECT_ macros. 

## Floating-Point Assertions
- Almost Equal: `EXPECT_FLOAT_EQ(expected, actual);` | e.g.: `EXPECT_FLOAT_EQ(3.14f, myFloat);`
- Almost Equal: `EXPECT_DOUBLE_EQ(expected, actual);` | e.g.: `EXPECT_DOUBLE_EQ(3.14159, myDouble);`
- Near: `EXPECT_NEAR(val1, val2, abs_error);` | e.g.: `EXPECT_NEAR(1.0, 1.0001, 0.001);`

## String Assertions
- C-string Equality: `EXPECT_STREQ("expected", actual_c_string);` | e.g.: `EXPECT_STREQ("hello", getGreeting());`
- C-string Non-equality: `EXPECT_STRNE("val1", "val2");` | e.g.: `EXPECT_STRNE("hello", "world");`
- Substring: `EXPECT_HAS_SUBSTR("substring", main_string);` | e.g.: `EXPECT_HAS_SUBSTR("world", "hello world");`

## Test Fixtures
- Define fixture:
  ```cpp
  class MyTestFixture : public ::testing::Test {
  protected:
    void SetUp() override { /* initialization */ }
    void TearDown() override { /* cleanup */ }
    // Members
  };
  