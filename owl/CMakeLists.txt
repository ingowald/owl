# ======================================================================== #
# Copyright 2019-2020 Ingo Wald                                            #
#                                                                          #
# Licensed under the Apache License, Version 2.0 (the "License");          #
# you may not use this file except in compliance with the License.         #
# You may obtain a copy of the License at                                  #
#                                                                          #
#     http://www.apache.org/licenses/LICENSE-2.0                           #
#                                                                          #
# Unless required by applicable law or agreed to in writing, software      #
# distributed under the License is distributed on an "AS IS" BASIS,        #
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. #
# See the License for the specific language governing permissions and      #
# limitations under the License.                                           #
# ======================================================================== #

set(CUDA_USE_STATIC_CUDA_RUNTIME ON)

include_directories(${PROJECT_SOURCE_DIR}/owl/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

cuda_include_directories(${PROJECT_SOURCE_DIR}/owl/include)
cuda_include_directories(${CMAKE_CURRENT_SOURCE_DIR})


add_definitions(${OWL_CXX_FLAGS})


set(INTERFACE_POSITION_INDEPENDENT_CODE ON)

set(CUDA_SEPARABLE_COMPILATION ON)
set(CUDA_VERBOSE_BUILD ON)
if (NOT WIN32)
set(CUDA_NVCC_FLAGS --compiler-options -fPIC)
endif()

include_directories(${PROJECT_SOURCE_DIR}/)
include_directories(${PROJECT_SOURCE_DIR}/owl/include)

cuda_include_directories(${PROJECT_SOURCE_DIR}/owl/include)

set(OWL_SOURCES

  # -------------------------------------------------------
  # infrastructure
  # -------------------------------------------------------
  include/owl/owl.h
  include/owl/owl_host.h
  include/owl/owl_device.h
  include/owl/common/arrayND/array2D.h
  include/owl/common/arrayND/array3D.h
  include/owl/common/math/AffineSpace.h
  include/owl/common/math/box.h
  include/owl/common/math/constants.h
  include/owl/common/math/fixedpoint.h
  include/owl/common/math/LinearSpace.h
  include/owl/common/math/Quaternion.h
  include/owl/common/math/random.h
  include/owl/common/math/vec/compare.h
  include/owl/common/math/vec/functors.h
  include/owl/common/math/vec/rotate.h
  include/owl/common/math/vec.h
  include/owl/common/owl-common.h
  include/owl/common/parallel/parallel_for.h
  include/owl/owl.h
  include/owl/owl_device.h
  include/owl/owl_device_buffer.h
  include/owl/owl_host.h
  helper/cuda.h
  helper/optix.h
  common.h

  cmake/configure_tbb.cmake
  cmake/configure_owl.cmake
  cmake/configure_optix.cmake
  cmake/configure_glfw.cmake
  cmake/FindOptiX.cmake

  # -------------------------------------------------------
  # API layer
  # -------------------------------------------------------
  APIContext.h
  APIContext.cpp
  APIHandle.h
  APIHandle.cpp
  impl.cpp
  
  # -------------------------------------------------------
  # data handling
  # -------------------------------------------------------
  Buffer.h
  Buffer.cpp
  Texture.h
  Texture.cpp

  # -------------------------------------------------------
  # base classes
  # -------------------------------------------------------
  Object.h
  Object.cpp
  Module.h
  Module.cpp
  RegisteredObject.h
  RegisteredObject.cpp
  DeviceContext.h
  DeviceContext.cpp
  
  ObjectRegistry.h
  ObjectRegistry.cpp
  Context.h
  Context.cpp

  # -------------------------------------------------------
  # program types
  # -------------------------------------------------------
  SBTObject.h
  SBTObject.cpp
  RayGen.h
  RayGen.cpp
  LaunchParams.h
  LaunchParams.cpp
  MissProg.h
  MissProg.cpp
  Variable.h
  Variable.cpp
  
  # -------------------------------------------------------
  # geometries
  # -------------------------------------------------------
  Geometry.h
  Geometry.cpp
  Triangles.h
  Triangles.cu
  UserGeom.h
  UserGeom.cu

  # -------------------------------------------------------
  # accel structures
  # -------------------------------------------------------
  Group.h
  Group.cpp
  InstanceGroup.h
  InstanceGroup.cpp
  TrianglesGeomGroup.h
  TrianglesGeomGroup.cpp
  UserGeomGroup.h
  UserGeomGroup.cpp
)

# Nate: 05/31/21: cuda_add_library for some reason is always out of date on 
# windows, causing owl and all dependencies to rebuild every compile...
# This rebuild issue doesn't seem to happen with native CUDA language support, 
# so I'm switching over to that.
add_library(owl_static STATIC
  ${OWL_SOURCES} 
)

target_link_libraries(owl_static
   ${CUDA_LIBRARIES}
   ${CUDA_CUDA_LIBRARY}
   ${CMAKE_THREAD_LIBS_INIT}
  )


set_target_properties(owl_static PROPERTIES POSITION_INDEPENDENT_CODE ON)

# -------------------------------------------------------
# export those back to our parent:
# -------------------------------------------------------
set(OWL_INCLUDES ${OWL_INCLUDES} PARENT_SCOPE)
set(OWL_LIBRARIES ${OWL_LIBRARIES} PARENT_SCOPE)
