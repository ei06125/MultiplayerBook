cmake_minimum_required(VERSION 3.21)

project(
  MultiplayerBookProject
  VERSION 0.0.1
  LANGUAGES CXX
)

set(ROOT_DIR ${CMAKE_CURRENT_LIST_DIR})
# set(CMAKE_CURRENT_LOG_LEVEL 2)
include(${ROOT_DIR}/tools/cmake/Modules/BuildToolsMain.cmake)

cmake_host_system_information(RESULT OS_NAME_RESULT QUERY OS_NAME)
cmake_host_system_information(RESULT OS_PLATFORM_RESULT QUERY OS_PLATFORM)
log_debug("OS_NAME_RESULT: ${OS_NAME_RESULT}")
log_debug("OS_PLATFORM_RESULT: ${OS_PLATFORM_RESULT}")

# Surpress CTest Targets (Continuous, Experimental, Nightly)
set_property(GLOBAL PROPERTY CTEST_TARGETS_ADDED 1)

# Surpresses MSVC Target ZERO_CHECK
set(CMAKE_SUPPRESS_REGENERATION ON)
set(INSTALL_GTEST OFF)
set(CMAKE_VERBOSE_MAKEFILE OFF)
set(CMAKE_DEBUG_POSTFIX d)
set(CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS OFF)

if(OS_NAME STREQUAL "Windows")
  set(RUNTIME_OUTPUT ${ROOT_DIR}/out/Windows)
  set(LIBRARY_OUTPUT ${ROOT_DIR}/out/Windows)
  set(ARCHIVE_OUTPUT ${ROOT_DIR}/out/Windows)
elseif(OS_NAME STREQUAL "Linux")
  set(RUNTIME_OUTPUT ${ROOT_DIR}/out/Linux/${CMAKE_BUILD_TYPE}/bin)
  set(LIBRARY_OUTPUT ${ROOT_DIR}/out/Linux/${CMAKE_BUILD_TYPE}/lib)
  set(ARCHIVE_OUTPUT ${ROOT_DIR}/out/Linux/${CMAKE_BUILD_TYPE}/lib)
else() # MacOS = elseif(OS_NAME STREQUAL "Darwin") maybe?
  set(RUNTIME_OUTPUT ${ROOT_DIR}/out/MacOS/${CMAKE_BUILD_TYPE}/bin)
  set(LIBRARY_OUTPUT ${ROOT_DIR}/out/MacOS/${CMAKE_BUILD_TYPE}/lib)
  set(ARCHIVE_OUTPUT ${ROOT_DIR}/out/MacOS/${CMAKE_BUILD_TYPE}/lib)
endif()
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${RUNTIME_OUTPUT})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIBRARY_OUTPUT})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${ARCHIVE_OUTPUT})

# ============================================================================
# Building
# ============================================================================

# ----------------------------------------------------------------------------
# Dependencies
# ----------------------------------------------------------------------------

# This module enables populating content at configure time. Check more at:
# https://cmake.org/cmake/help/v3.21/module/FetchContent.html
include(FetchContent)

# ----------------------------------------------------------------------------
# global_settings defines a options/features/definitions propagation library
# ----------------------------------------------------------------------------
log_info("Configuring global_settings")
add_library(global_settings INTERFACE)
target_compile_features(global_settings INTERFACE cxx_std_20)
if(MSVC)
  target_compile_definitions(
    global_settings
    INTERFACE #
              NOMINMAX # defining NOMINMAX disables MSVC min max c++ macros
              WIN32_LEAN_AND_MEAN # excludes APIs such as Cryptography, DDE,
                                  # RPC, Shell, and Windows Sockets.
  )
endif()
target_precompile_headers(
  global_settings
  INTERFACE # C Standard Library <cstdint> <cstdlib> # C++ Standard Library
  <algorithm>
  <exception>
  <filesystem>
  <iostream>
  <string>
  <string_view>
  <vector>
)
if(MSVC)
  target_precompile_headers(
    global_settings INTERFACE <Windows.h><winsock2.h> <iphlpapi.h> <ws2tcpip.h>
  )
else()
  target_precompile_headers(
    global_settings INTERFACE <sys/socket.h> <netdb.h> <ifaddrs.h>
  )
endif()

if(MSVC)
  target_link_libraries(global_settings INTERFACE ws2_32 iphlpapi)
endif()

set_project_warnings(global_settings)

# ----------------------------------------------------------------------------
# Libs
# ----------------------------------------------------------------------------

# add_subdirectory(lib)

# ----------------------------------------------------------------------------
# Apps
# ----------------------------------------------------------------------------

# add_subdirectory(src)

# ============================================================================
# Tests
# ============================================================================

add_subdirectory(tests)

# ============================================================================
# Install: TODO
# ============================================================================
