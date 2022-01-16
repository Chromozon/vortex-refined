cmake_minimum_required(VERSION 3.21)

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    message(FATAL_ERROR "In-source builds not allowed. Run CMake from the build directory. You may need to remove CMakeCache.txt.")
endif()

# Project
project(vortex_refined LANGUAGES C CXX)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 17)

# Force only 32-bit compile
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    message(FATAL_ERROR "This project only supports 32-bit compile. Select a 32-bit compiler toolchain.")
endif()
if(UNIX)
    set(CMAKE_C_FLAGS "-m32")
    set(CMAKE_CXX_FLAGS "-m32")
endif()

if(WIN32)
    set(LIB_NAME "gamex86")
elseif(UNIX)
    set(LIB_NAME "gamei386")
endif()

# Stop CMake from preprending "lib" to the library name
set(CMAKE_SHARED_LIBRARY_PREFIX "")

# Source files
file(GLOB_RECURSE VRX_FILES "./src/*.c")
file(GLOB_RECURSE VRX_FILES_CPP "./src/*.cpp")

include_directories("./src/" "./src/*/" "./src/*/*/")

if(WIN32)
    set(DEF_FILE "./src/game.def")
endif()

add_library(${LIB_NAME} SHARED)

target_sources(${LIB_NAME} PRIVATE
    ${VRX_FILES}
    ${VRX_FILES_CPP}
    ${DEF_FILE}
)

# Third-party sources
target_sources(${LIB_NAME} PRIVATE "./thirdparty/sqlite-amalgamation-3370200/sqlite3.c")
include_directories(AFTER "./thirdparty/sqlite-amalgamation-3370200/")

target_compile_definitions(${LIB_NAME} PRIVATE GDS_NOMULTITHREADING=1)
