cmake_minimum_required(VERSION 3.5)

project(EzGz LANGUAGES CXX)

# Allow overriding the C++ standard via command line (default is C++20)
set(CMAKE_CXX_STANDARD 20 CACHE STRING "C++ standard (e.g., 17 or 20)")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF) # Use -std=c++XX instead of -std=gnu++XX

# Detect compiler type
if (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    if (CMAKE_SYSTEM_NAME STREQUAL "Darwin")
        message(STATUS "Detected AppleClang on macOS")
    else()
        message(STATUS "Detected Clang")
    endif()
elseif (CMAKE_CXX_COMPILER_ID MATCHES "AppleClang")
    message(STATUS "Detected AppleClang on macOS")
elseif (CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    message(STATUS "Detected MSVC on Windows")
else()
    message(STATUS "Using unknown compiler")
endif()

# Compiler-specific options
if (CMAKE_CXX_COMPILER_ID MATCHES "AppleClang|Clang")
    # Extra warnings for Clang/AppleClang
    set(COMMON_WARNINGS "-Wall" "-Wextra" "-Wpedantic")
elseif (CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    # Extra warnings for MSVC
    set(COMMON_WARNINGS "/W4")
    # Enable proper reporting of __cplusplus in MSVC
    add_compile_options(/Zc:__cplusplus)    
    # Suppress warning C5051 when using C++17 with MSVC (unknown )
    if (CMAKE_CXX_STANDARD EQUAL 17)
        add_compile_options(/wd5051)
    endif()
endif()

# Set output directory for executables
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Add executables
add_executable(ezgz_test ezgz_test.cpp)
add_executable(ezgz_decompress ezgz_decompress.cpp)

# Apply compiler options
if (COMMON_WARNINGS)
    target_compile_options(ezgz_test PRIVATE ${COMMON_WARNINGS} "-g")
    target_compile_options(ezgz_decompress PRIVATE ${COMMON_WARNINGS} "-g")
endif()

# Add threading support
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
target_link_libraries(ezgz_test PRIVATE Threads::Threads)
target_link_libraries(ezgz_decompress PRIVATE Threads::Threads)
