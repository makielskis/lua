cmake_minimum_required(VERSION 2.6)
project(lua)

if(NOT MSVC AND NOT MINGW)
  # Enable building a shared library.
  add_definitions("-fPIC")
endif()

include_directories("src")
set(SOURCES
  src/lapi.c
  src/lcode.c
  src/lctype.c
  src/ldebug.c
  src/ldo.c
  src/ldump.c
  src/lfunc.c
  src/lgc.c
  src/llex.c
  src/lmem.c
  src/lobject.c
  src/lopcodes.c
  src/lparser.c
  src/lstate.c
  src/lstring.c
  src/ltable.c
  src/ltm.c
  src/lundump.c
  src/lvm.c
  src/lzio.c
  src/lauxlib.c
  src/lbaselib.c
  src/lbitlib.c
  src/lcorolib.c
  src/ldblib.c
  src/liolib.c
  src/lmathlib.c
  src/loslib.c
  src/lstrlib.c
  src/ltablib.c
  src/loadlib.c
  src/linit.c
)
set_source_files_properties(${SOURCES} PROPERTIES LANGUAGE CXX)
add_library(lua STATIC ${SOURCES})

if (NOT MINGW AND NOT ANDROID AND NOT CROSS_ARM)
  set_source_files_properties(src/luac.c PROPERTIES LANGUAGE CXX)
  add_executable(luac EXCLUDE_FROM_ALL src/luac.c ${SOURCES})
  if (ARCH_32)
    set_target_properties(luac PROPERTIES COMPILE_FLAGS "-m32")
    set_target_properties(luac PROPERTIES LINK_FLAGS "-m32")
    message("compiling with -m32")
  else()
    message("compiling host machine architecture (probably x86_64)")
  endif()
  get_property(luac_location TARGET luac PROPERTY LOCATION)
  set(luac_location ${luac_location} PARENT_SCOPE)
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
