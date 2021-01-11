cmake_minimum_required(VERSION 3.10)
project(lua)

if(NOT MSVC AND NOT MINGW)
  # Enable building a shared library.
  add_definitions("-fPIC")
endif()

include_directories("src")
set(SOURCES
  src/lapi.cc
  src/lcode.cc
  src/lctype.cc
  src/ldebug.cc
  src/ldo.cc
  src/ldump.cc
  src/lfunc.cc
  src/lgc.cc
  src/llex.cc
  src/lmem.cc
  src/lobject.cc
  src/lopcodes.cc
  src/lparser.cc
  src/lstate.cc
  src/lstring.cc
  src/ltable.cc
  src/ltm.cc
  src/lundump.cc
  src/lvm.cc
  src/lzio.cc
  src/lauxlib.cc
  src/lbaselib.cc
  src/lbitlib.cc
  src/lcorolib.cc
  src/ldblib.cc
  src/liolib.cc
  src/lmathlib.cc
  src/loslib.cc
  src/lstrlib.cc
  src/ltablib.cc
  src/loadlib.cc
  src/linit.cc
)
add_library(lua STATIC ${SOURCES})
target_include_directories(lua PUBLIC src)

if (NOT MINGW AND NOT ANDROID AND NOT CROSS_ARM)
  add_executable(luac EXCLUDE_FROM_ALL src/luac.cc ${SOURCES})
  if (ARCH_32)
    set_target_properties(luac PROPERTIES COMPILE_FLAGS "-m32")
    set_target_properties(luac PROPERTIES LINK_FLAGS "-m32")
    message("compiling with -m32")
  else()
    message("compiling host machine architecture (probably x86_64)")
  endif()
  set(luac_location $<TARGET_FILE:luac> PARENT_SCOPE)
else()
  if (MINGW OR ANDROID OR CROSS_ARM)
    set(ARCH "-DARCH_32:boolean=true")
  endif()

  set(luac_location ${CMAKE_BINARY_DIR}/luac/luac PARENT_SCOPE)
  add_custom_target(luac
    rm -rf ${CMAKE_BINARY_DIR}/luac &&
    mkdir ${CMAKE_BINARY_DIR}/luac &&
    cd ${CMAKE_BINARY_DIR}/luac &&
    cmake ${ARCH} ${CMAKE_CURRENT_SOURCE_DIR} &&
    make luac
  )
endif()
