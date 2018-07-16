cmake_minimum_required(VERSION 2.8)

project(haha)
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "{CMAKE_CXX_FLAGS} -O0 -g -Wall -std=c++11")
#
#project include, source dir
include_directory(${PROJECT_SOURCE_DIR}/include)
set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)
aux_source_directory(${SRC_DIR} SRC_LIST)
#find cuda 
find_package(CUDA 8.0 REQUIRED)
include_directories(${CUDA_INCLUDE_DIRS})
file(GLOB, CUDA_FILES, "${SRC_DIR}/*.cu")
message("CUDA files:${CUDA_FILES}")
list(APPEND CUDA_NVCC_FLAGS "-arch=sm_61")
CUDA_COMPILE(CU_O ${CUDA_FILES})
link_libraries(${CUDA_LIBRARIES})
# other libs ....
set(main main.cpp)
add_executable(haha ${main} ${SRC_LIST} ${CU_O})
target_link_libraries(haha  ${GTEST_LIBRARIES}) #you may use gtest 
 
