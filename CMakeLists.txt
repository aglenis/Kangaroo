cmake_minimum_required( VERSION 2.8 )
project( Kangaroo )

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules/")

option(BUILD_APPLICATIONS "Build Applications" ON)
option(BUILD_SHARED_LIBS "Build Shared Library" ON)

# Overide with cmake -DCMAKE_BUILD_TYPE=Debug {dir}
if( NOT CMAKE_BUILD_TYPE )
   set( CMAKE_BUILD_TYPE Release )
endif()

# Platform configuration vars
include(SetPlatformVars)

# Disable unknown pragma warnings - pragma unrolls are for NVCC.
if(MSVC)
    add_definitions( "/wd4068" )
else()
    add_definitions( "-Wno-unknown-pragmas" )
endif()

add_subdirectory(kangaroo)

if(BUILD_APPLICATIONS)
  add_subdirectory(applications)
endif()

