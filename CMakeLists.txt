cmake_minimum_required(VERSION 3.5)
project(mmdeploy_tensorrt_ops)

find_package(ament_cmake REQUIRED)
find_package(tensorrt_cmake_module REQUIRED)
find_package(TENSORRT REQUIRED)

set(extra_cmake_args)
if(DEFINED CMAKE_BUILD_TYPE)
  list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
endif()

list(APPEND extra_cmake_args -DCMAKE_CXX_COMPILER=g++)
list(APPEND extra_cmake_args -DMMDEPLOY_TARGET_BACKENDS=trt)
list(APPEND extra_cmake_args -DMMDEPLOY_TARGET_DEVICES=cuda)
list(APPEND extra_cmake_args -DTENSORRT_DIR=${TENSORRT_ROOT})

include(ExternalProject)

externalproject_add(mmdeploy-v1.3.0
  GIT_REPOSITORY https://github.com/open-mmlab/mmdeploy.git
  GIT_TAG 1132e827d73b308f04c6d636298ff3a7094c585a
  PREFIX mmdeploy-v1.3.0
  TIMEOUT 600
  CMAKE_ARGS
    ${extra_cmake_args}
    # PATCH_COMMAND
    # git apply ${CMAKE_CURRENT_SOURCE_DIR}/install.patch
)

externalproject_get_property(mmdeploy-v1.3.0 BINARY_DIR)

# The external project will install to the build folder, but we'll install that on make install.
install(
  FILES
    ${BINARY_DIR}/lib/${CMAKE_SHARED_LIBRARY_PREFIX}mmdeploy_tensorrt_ops${CMAKE_SHARED_LIBRARY_SUFFIX}
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}/lib/${PROJECT_NAME}
)

ament_package()
