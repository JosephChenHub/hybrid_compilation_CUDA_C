# hybrid_compilation_CUDA_C
This demo shows how to compile a project which contains both CUDA and c++ codes in multiple files. I would thank to this blog [https://vivekvidyasagaran.weebly.com/moose/compiling-cuda-code-along-with-other-c-code](https://vivekvidyasagaran.weebly.com/moose/compiling-cuda-code-along-with-other-c-code).
## starting write a *CMakeLists.txt*:
suppose your project files is 
> main.cpp (main function)
> CMakeLists.txt
> build
> include (some headers)
>---- cu_mat.hpp (suppose you defined a class  ***gpu_mat***)
>---- foo.hpp 
> src (source folder)
>---- foo.cpp  (suppose you called ***gpu_mat***)  
>---- cu_mat.cu (you call **CUDA** or **CUBLAS** etc. gpu libs to implement the class **gpu_mat**)
then you want to compile the project , however **CUDA** apis must be compiled by ***NVCC*** which is diffent from ***g++***!  Two solution:
1. split the code which contains gpu or not , then compile the gpu part code with ***NVCC*** to generate ***static*** or ***dynamic*** libraries, use ***g++*** or ***cxx*** to generate **.obj** files for host code, then **link** the ***static*** or ***dynamic*** libraries to target.
2. for **CUDA** apis using ***NVCC*** to generate **.obj** files, for host code using ***g++*** to get **.obj** files, then ***link*** them together.

Here I exploit the second method, a simple ***CMakeLists.txt*** can be written as 
> cmake_minimum_required(VERSION 2.8)
> project(haha)
> set(CMAKE_BUILD_TYPE "Debug")
> set(CMAKE_CXX_FLAGS_DEBUG "{CMAKE_CXX_FLAGS} -O0 -g -Wall -std=c++11")
>\#
>\#project include, source dir
>include_directory(${PROJECT_SOURCE_DIR}/include)
>set(SRC_DIR ${PROJECT_SOURCE_DIR}/src)
>aux_source_directory(${SRC_DIR} SRC_LIST)
>\#find cuda 
>find_package(CUDA 8.0 REQUIRED)
>include_directories(${CUDA_INCLUDE_DIRS})
>file(GLOB, CUDA_FILES, "${SRC_DIR}/*.cu")
>message("CUDA files:${CUDA_FILES}")
>list(APPEND CUDA_NVCC_FLAGS "-arch=sm_61")
>CUDA_COMPILE(CU_O ${CUDA_FILES})
>link_libraries(${CUDA_LIBRARIES})
>\# other libs ....
> set(main main.cpp)
> add_executable(haha ${main} ${SRC_LIST} ${CU_O})
> target_link_libraries(haha  ${GTEST_LIBRARIES}) #you may use gtest 
 
then you can play happy with ***CUDA*** and ***C++***...