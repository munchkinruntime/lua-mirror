cmake_minimum_required(
  VERSION "3.13"
  FATAL_ERROR
)

project(
  lua
  LANGUAGES
    "C"
)

set(
  _build_is_debug
  OFF
)

if(CMAKE_BUILD_TYPE STREQUAL "")
  set(
    CMAKE_BUILD_TYPE
    "RelWithDebInfo"
    CACHE
    STRING
    "The build type for the project (valid options: \"Debug\", \"MinSizeRel\", \"Release\", \"RelWithDebInfo\")"
    FORCE
  )

  if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(
      _build_is_debug
      ON
    )
  endif()
endif()

option(
  BUILD_SHARED_LIBS
  "Build shared libraries instead of static libraries"
  OFF
)

option(
  LUA_ENABLE_ASSERTIONS
  "Enable internal assertions"
  ${_build_is_debug}
)

option(
  LUA_USE_32_BIT_NUMBERS
  "Use 32-bit numbers (floats and integers) in Lua instead of 64-bit numbers"
  OFF
)

set(
  _build_lua_use_32_bit_numbers_value
  0
)

if(LUA_USE_32_BIT_NUMBERS)
  set(
    _build_lua_use_32_bit_numbers_value
    1
  )
endif()

add_library(lua)

configure_file(
  "${CMAKE_SOURCE_DIR}/luaconf.h.in"
  "${CMAKE_SOURCE_DIR}/luaconf.h"
  @ONLY
)

set_target_properties(
  lua
  PROPERTIES
    C_EXTENSIONS
      ON
    C_STANDARD
      "99"
    C_STANDARD_REQUIRED
      ON
    C_VISIBILITY_PRESET
      "default"
    PUBLIC_HEADER
      "${CMAKE_SOURCE_DIR}/lua.h;${CMAKE_SOURCE_DIR}/luaconf.h;${CMAKE_SOURCE_DIR}/lualib.h;${CMAKE_SOURCE_DIR}/lauxlib.h"
)

if(BUILD_SHARED_LIBS)
  set_target_properties(
    lua
    PROPERTIES
      POSITION_INDEPENDENT_CODE ON
  )
endif()

if (LUA_ENABLE_ASSERTIONS)
  target_compile_definitions(
    lua
    PRIVATE
      "LUA_USE_APICHECK"
      "LUAI_ASSERT"
  )
endif()

target_compile_definitions(
  lua
  PRIVATE
    "_POSIX_C_SOURCE=200809"
    "LUA_USE_POSIX"
)

target_compile_options(
  lua
  PRIVATE
    "-Wall"
    "-Wextra"
)

target_include_directories(
  lua
  PRIVATE
    ${CMAKE_SOURCE_DIR}
)

target_sources(
  lua
  PRIVATE
    "${CMAKE_SOURCE_DIR}/lapi.c"
    "${CMAKE_SOURCE_DIR}/lauxlib.c"
    "${CMAKE_SOURCE_DIR}/lbaselib.c"
    "${CMAKE_SOURCE_DIR}/lcode.c"
    "${CMAKE_SOURCE_DIR}/lcorolib.c"
    "${CMAKE_SOURCE_DIR}/lctype.c"
    "${CMAKE_SOURCE_DIR}/ldblib.c"
    "${CMAKE_SOURCE_DIR}/ldebug.c"
    "${CMAKE_SOURCE_DIR}/ldo.c"
    "${CMAKE_SOURCE_DIR}/ldump.c"
    "${CMAKE_SOURCE_DIR}/lfunc.c"
    "${CMAKE_SOURCE_DIR}/lgc.c"
    "${CMAKE_SOURCE_DIR}/linit.c"
    "${CMAKE_SOURCE_DIR}/liolib.c"
    "${CMAKE_SOURCE_DIR}/llex.c"
    "${CMAKE_SOURCE_DIR}/lmathlib.c"
    "${CMAKE_SOURCE_DIR}/lmem.c"
    "${CMAKE_SOURCE_DIR}/loadlib.c"
    "${CMAKE_SOURCE_DIR}/lobject.c"
    "${CMAKE_SOURCE_DIR}/lopcodes.c"
    "${CMAKE_SOURCE_DIR}/loslib.c"
    "${CMAKE_SOURCE_DIR}/lparser.c"
    "${CMAKE_SOURCE_DIR}/lstate.c"
    "${CMAKE_SOURCE_DIR}/lstring.c"
    "${CMAKE_SOURCE_DIR}/lstrlib.c"
    "${CMAKE_SOURCE_DIR}/ltable.c"
    "${CMAKE_SOURCE_DIR}/ltablib.c"
    "${CMAKE_SOURCE_DIR}/ltests.c"
    "${CMAKE_SOURCE_DIR}/ltm.c"
    "${CMAKE_SOURCE_DIR}/lundump.c"
    "${CMAKE_SOURCE_DIR}/lutf8lib.c"
    "${CMAKE_SOURCE_DIR}/lvm.c"
    "${CMAKE_SOURCE_DIR}/lzio.c"
)

target_link_libraries(
  lua
  PRIVATE
    "m"
)

if(ON)
  include(GNUInstallDirs)

  install(
    TARGETS
      lua
      ARCHIVE
        DESTINATION
          "${CMAKE_INSTALL_FULL_LIBDIR}"
      LIBRARY
        DESTINATION
          "${CMAKE_INSTALL_FULL_LIBDIR}"
      PUBLIC_HEADER
        DESTINATION
          "${CMAKE_INSTALL_FULL_INCLUDEDIR}"
  )
endif()
