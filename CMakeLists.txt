####################################################################################
# START 1. Basic setup for cmake
####################################################################################

# basic setup for cmake
cmake_minimum_required(VERSION 3.18 FATAL_ERROR)

if(POLICY CMP0074)
  cmake_policy(SET CMP0074 NEW)
endif()

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_INCLUDE_DIRECTORIES_PROJECT_BEFORE ON)
set(CMAKE_COLOR_MAKEFILE ON)
set(CMAKE_CXX_STANDARD_REQUIRED True)
# Disable gnu exentions
set(CMAKE_CXX_EXTENSIONS ON)

# Define the project
project("dedisp" VERSION 1.0.0 LANGUAGES CXX CUDA C)

# DEDISP may be built to run using CUDA. Future version may be
# written for HIP or SYCL, which we call the
# Target type. By default, the target is CUDA.
if(DEFINED ENV{DEDISP_TARGET})
  set(DEFTARGET $ENV{DEDISP_TARGET})
else()
  set(DEFTARGET "CUDA")
endif()

set(VALID_TARGET_TYPES CUDA) #HIP SYCL
set(DEDISP_TARGET_TYPE
  "${DEFTARGET}"
  CACHE STRING "Choose the type of target, options are: ${VALID_TARGET_TYPES}")
set_property(CACHE DEDISP_TARGET_TYPE PROPERTY STRINGS CUDA)

# CUDA specific part of CMakeLists
#set(CMAKE_CUDA_EXTENSIONS OFF)
find_package(CUDAToolkit REQUIRED)

string(TOUPPER ${DEDISP_TARGET_TYPE} CHECK_TARGET_TYPE)
list(FIND VALID_TARGET_TYPES ${CHECK_TARGET_TYPE} TARGET_TYPE_VALID)

if(TARGET_TYPE_VALID LESS 0)
  message(SEND_ERROR "Please specify a valid DEDISP_TARGET_TYPE type! Valid target types are:" "${VALID_TARGET_TYPES}")
endif()

# Git
find_package(Git)
if(GIT_FOUND)
  execute_process(
    COMMAND ${GIT_EXECUTABLE} show
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    RESULT_VARIABLE IS_GIT_REPOSIITORY
    OUTPUT_QUIET ERROR_QUIET)
  if(${IS_GIT_REPOSIITORY} EQUAL 0)
    execute_process(
      COMMAND ${GIT_EXECUTABLE} describe --abbrev=0
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
      OUTPUT_VARIABLE GITTAG
      OUTPUT_STRIP_TRAILING_WHITESPACE)
    # we use git rev-list and pipe that through wc here. Newer git versions support --count as option to rev-list but
    # that might not always be available
    execute_process(
      COMMAND ${GIT_EXECUTABLE} rev-list ${GITTAG}..HEAD
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
      COMMAND wc -l
      OUTPUT_VARIABLE GITCOUNT
      OUTPUT_STRIP_TRAILING_WHITESPACE)
    execute_process(
      COMMAND ${GIT_EXECUTABLE} describe --match 1 --always  --long --dirty
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
      OUTPUT_VARIABLE GITVERSION
      OUTPUT_STRIP_TRAILING_WHITESPACE)
  endif()
endif(GIT_FOUND)

option(DEDISP_TEXTURE "Use texture support (reference/obj)" ON)
add_compile_definitions(DEDISP_USE_TEXTURE=${DEDISP_USE_TEXTURE} DEDISP_TEXTURE)

option(DEDISP_BUILD_TESTS "Build test suite" ON)
option(BUILD_SHARED_LIBS "Build using shared libraries" OFF)

# Print the configuration details to stdout
message(STATUS "")
message(STATUS "${PROJECT_NAME} ${PROJECT_VERSION} (${GITVERSION}) **")
message(STATUS "cmake version: ${CMAKE_VERSION}")
message(STATUS "Source location: ${CMAKE_SOURCE_DIR}")
message(STATUS "Build location: ${CMAKE_BINARY_DIR}")
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "DEDISP target: ${DEDISP_TARGET_TYPE}")
message(STATUS "DEDISP texture: ${DEDISP_TEXTURE}")
message(STATUS "DEDISP build tests: ${DEDISP_BUILD_TESTS}")
message(STATUS "DEDISP build shared libs: ${BUILD_SHARED_LIBS}")

# Add src, tests
add_subdirectory(src)

if(DEDISP_BUILD_TESTS)
  add_subdirectory(example)
endif()

# Install project cmake targets
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${PROJECT_NAME}-config-version.cmake
  VERSION ${DEDISP_VERSION}
  COMPATIBILITY AnyNewerVersion
)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake
        DESTINATION ${CMAKE_INSTALL_PREFIX}/cmake/${PROJECT_NAME}
)
