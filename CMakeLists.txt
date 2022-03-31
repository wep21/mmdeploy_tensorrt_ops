cmake_minimum_required(VERSION 3.5)
project(mmdeploy_tensorrt_plugins)

find_package(ament_cmake REQUIRED)

set(extra_cmake_args)
if(DEFINED CMAKE_BUILD_TYPE)
  list(APPEND extra_cmake_args -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
endif()

list(APPEND extra_cmake_args -DCMAKE_CXX_COMPILER=g++)
list(APPEND extra_cmake_args -DMMDEPLOY_TARGET_BACKENDS=trt)
list(APPEND extra_cmake_args -DMMDEPLOY_TARGET_DEVICES=cuda)

include(ExternalProject)

externalproject_add(mmdeploy-v0.3.0
  GIT_REPOSITORY https://github.com/open-mmlab/mmdeploy.git
  GIT_TAG 34879e638cc2db511e798a376b9a4b9932660fe1
  PREFIX mmdeploy-v0.3.0
  TIMEOUT 600
  CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/mmdeploy_install
    ${extra_cmake_args}
  PATCH_COMMAND
    git apply ${CMAKE_CURRENT_SOURCE_DIR}/install.patch
)

# The external project will install to the build folder, but we'll install that on make install.
install(
  DIRECTORY
    ${CMAKE_CURRENT_BINARY_DIR}/mmdeploy_install/
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}
  USE_SOURCE_PERMISSIONS
)

ament_package()
