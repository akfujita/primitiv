cmake_minimum_required(VERSION 3.1.0 FATAL_ERROR)
set(CMAKE_POLICY_DEFAULT_CMP0048 NEW)

project(primitiv VERSION 0.1.0 LANGUAGES CXX)
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

option(PRIMITIV_BUILD_STATIC_LIBRARY "Builds static library." OFF)
option(PRIMITIV_BUILD_TESTS "Builds test binaries." OFF)
option(PRIMITIV_BUILD_TESTS_PROBABILISTIC "Builds test cases that probabilistically fails." OFF)
option(PRIMITIV_GTEST_SOURCE_DIR "Source directory of Google Test library." "")
option(PRIMITIV_USE_CACHE "Enables cached values in some functions but needs more memory." OFF)
option(PRIMITIV_USE_CUDA "Enables the CUDA backend." OFF)
option(PRIMITIV_USE_OPENCL "Enables the OpenCL backend." OFF)

# C++ version
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# compiler settings
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -Wall -Werror -fPIC")
elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -Wall -Werror -fPIC")
else()
  message(WARNING "primitiv may not support the detected compiler: ${CMAKE_CXX_COMPILER_ID}")
endif()

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
  ${CMAKE_CURRENT_BINARY_DIR}/config.h
)

# External packages.
if(PRIMITIV_USE_CUDA)
  find_package(CUDA REQUIRED)
endif()

if(PRIMITIV_USE_OPENCL)
  find_package(OpenCL REQUIRED)
  find_package(CLHPP REQUIRED)
  find_package(clBLAS REQUIRED)
endif()

# Include directories.
include_directories(
  ${PROJECT_SOURCE_DIR}
  ${PROJECT_BINARY_DIR})
if(PRIMITIV_USE_CUDA)
  include_directories(SYSTEM ${CUDA_INCLUDE_DIRS})
endif()
if(PRIMITIV_USE_OPENCL)
  include_directories(${CLHPP_INCLUDE_DIR})
endif()

# core library
add_subdirectory(primitiv)

# tests
if(PRIMITIV_BUILD_TESTS)
  enable_testing()
  add_subdirectory(test)
endif()
