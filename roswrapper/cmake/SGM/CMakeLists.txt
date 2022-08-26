cmake_minimum_required(VERSION 3.5.0)
project(SGM)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++17)
add_compile_options(-O3)

find_package(catkin_simple REQUIRED COMPONENTS)

find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})
find_package(CUDA REQUIRED)

include_directories(
  ${AIRSIM_ROOT}/SGM/include
)

set(CUDA_NVCC_FLAGS
        ${CUDA_NVCC_FLAGS};
        -O3
        -gencode=arch=compute_52,code=sm_52
        -gencode=arch=compute_61,code=sm_61
        -gencode=arch=compute_62,code=sm_62
        -gencode=arch=compute_75,code=sm_75
        )

message("CUDA Libs: ${CUDA_LIBRARIES}")
message("CUDA Headers: ${CUDA_INCLUDE_DIRS}")
message("CUDA curand library: ${CUDA_curand_LIBRARY}")

catkin_package(
        LIBRARIES
        CATKIN_DEPENDS
)
catkin_simple(ALL_DEPS_REQUIRED)

cuda_add_library(sgm_gpu
        ${AIRSIM_ROOT}/${PROJECT_NAME}/src/sgm_gpu/sgm_gpu.cu
        ${AIRSIM_ROOT}/${PROJECT_NAME}/src/sgm_gpu/costs.cu
        ${AIRSIM_ROOT}/${PROJECT_NAME}/src/sgm_gpu/hamming_cost.cu
        ${AIRSIM_ROOT}/${PROJECT_NAME}/src/sgm_gpu/left_right_consistency.cu
        ${AIRSIM_ROOT}/${PROJECT_NAME}/src/sgm_gpu/median_filter.cu)
