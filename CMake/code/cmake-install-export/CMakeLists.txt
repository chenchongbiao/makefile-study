cmake_minimum_required(VERSION 3.2)

project(install-export-demo C)

#设置install目录
set(CMAKE_INSTALL_PREFIX /home/user/win_samba/Demo4/build/install)
#提供GNU编码标准定义的安装目录变量
include(GNUInstallDirs)

#header files
file(GLOB HEADERS test_hello.h)

#source files
aux_source_directory(./ SRC_FILES)

#generate libtest_hello.so
add_library(${PROJECT_NAME} SHARED ${SRC_FILES})

message(STATUS "CMAKE_INSTALL_FULL_INCLUDEDIR = ${CMAKE_INSTALL_FULL_INCLUDEDIR}")
message(STATUS "CMAKE_PROJECT_NAME = ${CMAKE_PROJECT_NAME}")
message(STATUS "CMAKE_INSTALL_FULL_LIBDIR = ${CMAKE_INSTALL_FULL_LIBDIR}")
message(STATUS "CMAKE_CURRENT_LIST_FILE = ${CMAKE_CURRENT_LIST_FILE}")

#intall
install(FILES ${HEADERS} DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR}/${CMAKE_PROJECT_NAME})
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_FULL_LIBDIR} EXPORT ${PROJECT_NAME})
install(EXPORT ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_FULL_LIBDIR}/cmake/testHello)

#generate testHelloConfig.cmake
configure_file(./lib_config/helloConfig.cmake.in ${PROJECT_BINARY_DIR}/testHelloConfig.cmake @ONLY)
#install testHelloConfig.cmake
install(FILES ${PROJECT_BINARY_DIR}/testHelloConfig.cmake DESTINATION ${CMAKE_INSTALL_FULL_LIBDIR}/cmake/testHello)
