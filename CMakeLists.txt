#1.cmake verson，指定cmake的最小版本号
cmake_minimum_required(VERSION 2.8)

#2.project name，指定项目的名称，一般和项目的文件夹名称对应
project(face_detect C CXX)

#3.set environment variable，设置环境变量
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

find_package(OpenMP)
if(OpenMP_CXX_FOUND OR OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    message(STATUS "OpenMP_INCLUDE_DIRS: ${OpenMP_INCLUDE_DIRS}")
    message(STATUS "OpenMP_LIBS: ${OpenMP_LIBS}")
    message(STATUS "OpenMP_CXX_FLAGS: ${OpenMP_CXX_FLAGS}")
    message(STATUS "OpenMP_C_FLAGS: ${OpenMP_C_FLAGS}")
endif()

message(STATUS "CMAKE current List Dir: ${CMAKE_CURRENT_LIST_DIR}")


find_package( OpenCV REQUIRED )

if (NOT OpenCV_FOUND)
    message(FATAL_ERROR "opencv Not Found!")
else()
    message(STATUS "OpenCV_LIBS: ${OpenCV_LIBS}")
    message(STATUS "OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
endif (NOT OpenCV_FOUND)

#4.include头文件目录
include_directories( ${OpenCV_INCLUDE_DIRS}
                    ${CMAKE_CURRENT_LIST_DIR}/include
					${CMAKE_CURRENT_LIST_DIR}/3rdparty/include
                    )

#5.library目录及name名称
link_directories(${CMAKE_CURRENT_LIST_DIR}/3rdparty/lib)

list(APPEND FACE_DETECT_LINKER_LIBS mxnet)


#6.source directory源文件目录
file(GLOB MTCNN_SRC ${CMAKE_CURRENT_LIST_DIR}/libmtcnn/*.hpp
                    ${CMAKE_CURRENT_LIST_DIR}/libmtcnn/*.cpp
                    ${CMAKE_CURRENT_LIST_DIR}/src/*.cpp)
set(MTCNN_COMPILE_CODE ${MTCNN_SRC})

#7.1.add executable file，编译为可执行文件
add_executable(face_detect ${MTCNN_COMPILE_CODE})
#7.2.add library file，编译为动态库
# add_library(face_detect SHARED ${MTCNN_COMPILE_CODE})

#8.add link library，添加工程所依赖的库

target_link_libraries(face_detect mxnet ${OpenCV_LIBS})