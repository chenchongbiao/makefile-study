cmake_minimum_required(VERSION 3.0)

project(SOLIDERFIRE)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall") # 加入Wall参数 打印警告信息

set(CMAKE_BUILD_TYPE Debug) # 生成可以被调试的可执行文件

include_directories(${CMAKE_SOURCE_DIR}/include)

add_executable(my_cmake_exe main.cpp src/Gun.cpp src/Solider.cpp)