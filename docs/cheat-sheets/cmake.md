# C++ - CMake Cheatsheet

## Project Configuration & Building
- Configure project:  `cmake -S <source_dir> -B <build_dir>`      | e.g.: `cmake -S . -B build -DCMAKE_BUILD_TYPE:STRING=Debug`
- Build project:      `cmake --build <build_dir>`                 | e.g.: `cmake --build build --config Debug -j 8 --target my_app`
- Clean build files:  `cmake --build <build_dir> --target clean`  | e.g.: `cmake --build build --target clean`
- Install project:    `cmake --install <build_dir>`               | e.g.: `cmake --install build`, `cmake --install build --prefix /usr/local`

## Running Executables
- Run executable from build directory: `<build_dir>/<executable_name>`            | e.g.: `build/my_app`, `build/Debug/my_app.exe`
- Run executable via CMake: `cmake --build <build_dir> --target <exec_target> --` | e.g.: `cmake --build build --target my_app --`, 
                                                                                            `cmake --build build --target my_app -- ARGS "arg1 arg2"`

## CMakeLists.txt Cheat Sheet

## Global Project Configuration
- Define a boolean option: `option(BUILD_TESTS "Build tests" ON)`
- Set a variable: `set(SOURCE_FILES main.cpp utility.cpp)`
- Unset a variable: `unset(<variable> [CACHE])` | e.g.: `unset(MY_VARIABLE)`
- Conditional block: `if(<condition>)...endif()` | e.g.: `if(UNIX)`
- Add global include directories: `include_directories(${PROJECT_SOURCE_DIR}/external/include)`
- Add global preprocessor definitions: `add_definitions(-DGLOBAL_DEFINE)`
- Add global compile options: `add_compile_options(<option>...)` | e.g.: `add_compile_options(-Wno-missing-field-initializers)`

## Target Definition and Configuration
- Create an executable target: `add_executable(my_app main.cpp)`
- Create a library target: `add_library(my_shared_lib SHARED shared_lib.cpp)`
- Add sources to a target: `target_sources(my_app PRIVATE another_file.cpp)`
- Link libraries to a target: `target_link_libraries(<target> [PRIVATE|PUBLIC|INTERFACE] lib)`
- Add include directories for a target: `target_include_directories(<target> [PRIVATE|PUBLIC|INTERFACE] dir)` 
- Require C++ standard features for a target: `target_compile_features(<target> [PRIVATE|PUBLIC|INTERFACE] <feature>...)` 
- Add preprocessor definitions for a target: `target_compile_definitions(<target> [PRIVATE|PUBLIC|INTERFACE] <definition>...)`
- Add compiler options for a target: `target_compile_options(<target> [PRIVATE|PUBLIC|INTERFACE] <option>...)` 

## Installation Rules
- Install targets or files: `install(TARGETS <target>... DESTINATION <dir> [COMPONENT <comp>]...)` | e.g.: `install(TARGETS my_app DESTINATION bin)` | e.g.: `install(FILES header.h DESTINATION include)`