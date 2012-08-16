find_package(CUDA REQUIRED)
find_package(Boost REQUIRED)

set(CUDA_PROPAGATE_HOST_FLAGS off)
set(CUDA_NVCC_FLAGS "-arch=sm_20" )

if(NOT CUDA_npp_LIBRARY)
    # Hack for old FindCUDA to locate libnpp.so
    if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
        set(CUDA_npp_LIBRARY "/usr/local/cuda/lib/libnpp.dylib")
    else()
        set(CUDA_npp_LIBRARY "/usr/local/cuda/lib64/libnpp.so")
    endif()
endif()

list(APPEND SRC_H
    kangaroo.h
    Image.h sampling.h pixel_convert.h patch_score.h
    Mat.h MatUtils.h reweighting.h Pyramid.h
)

list(APPEND SRC_CU
    cu_anaglyph.cu cu_bilateral.cu cu_convert.cu cu_index_buffer.cu
    cu_model_refinement.cu cu_dense_stereo.cu cu_lookup_warp.cu
    cu_median.cu cu_normals.cu cu_openni.cu cu_plane_fit.cu
    cu_resample.cu cu_heightmap.cu
)

include_directories( ${Boost_INCLUDE_DIRS} )
cuda_add_library( ${LIBRARY_NAME} ${SRC_H} ${SRC_CU} )

##############################################################################
# Get full library name
GET_TARGET_PROPERTY( FULL_LIBRARY_NAME ${LIBRARY_NAME} LOCATION )
set( Kangaroo_LIBRARIES "${FULL_LIBRARY_NAME};${CUDA_LIBRARIES};${CUDA_npp_LIBRARY}" )
set( Kangaroo_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/..;${PROJECT_BINARY_DIR};${CUDA_INCLUDE_DIRS}")

##############################################################################
# Export config file for easy inclusion into other projects: FIND_PACKAGE( Pangolin )
CONFIGURE_FILE( ${CMAKE_CURRENT_SOURCE_DIR}/KangarooConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/KangarooConfig.cmake @ONLY IMMEDIATE )
EXPORT( PACKAGE kangaroo )