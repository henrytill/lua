CMAKE_MINIMUM_REQUIRED(VERSION 3.13)

PROJECT(lua VERSION 5.4.4 LANGUAGES C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED TRUE)

set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

# https://www.lua.org/manual/5.4/readme.html "Building Lua on other systems"
set(LIBRARY_FILES
    # Core
    lapi.c
    lcode.c
    lctype.c
    ldebug.c
    ldo.c
    ldump.c
    lfunc.c
    lgc.c
    llex.c
    lmem.c
    lobject.c
    lopcodes.c
    lparser.c
    lstate.c
    lstring.c
    ltable.c
    ltm.c
    lundump.c
    lvm.c
    lzio.c
    # Standard libraries
    lauxlib.c
    lbaselib.c
    lcorolib.c
    ldblib.c
    liolib.c
    lmathlib.c
    loadlib.c
    loslib.c
    lstrlib.c
    ltablib.c
    lutf8lib.c
    # Initialization
    linit.c)

list(TRANSFORM LIBRARY_FILES PREPEND "${SOURCE_DIR}/")

add_library(lua ${LIBRARY_FILES})
target_include_directories(lua INTERFACE ${SOURCE_DIR})

if(UNIX)
  target_compile_definitions(lua PUBLIC -DLUA_USE_POSIX)

  find_library(LIB_MATH NAMES m)
  if(LIB_MATH)
    target_link_libraries(lua ${LIB_MATH})
  endif()

  find_library(LIB_DLOPEN NAMES dl)
  if(LIB_DLOPEN)
    target_compile_definitions(lua PUBLIC -DLUA_USE_DLOPEN)
    target_link_libraries(lua ${LIB_DLOPEN})
  endif()
endif()

if(WIN32)
  target_compile_definitions(lua PRIVATE -D_CRT_SECURE_NO_WARNINGS)
endif()

if(WIN32 AND BUILD_SHARED_LIBS)
  message(STATUS "Building Lua as a shared library")
  target_compile_definitions(lua PUBLIC -DLUA_BUILD_AS_DLL)
endif()
