cmake_minimum_required(VERSION 3.5)

project(hello_world LANGUAGES CXX)

# Подключаем formatter_ex_lib
if(NOT TARGET formatter_ex)
    add_subdirectory(${CMAKE_SOURCE_DIR}/formatter_ex_lib 
                   ${CMAKE_CURRENT_BINARY_DIR}/formatter_ex_lib)
endif()

add_executable(hello_world hello_world.cpp)
target_link_libraries(hello_world PRIVATE formatter_ex)
