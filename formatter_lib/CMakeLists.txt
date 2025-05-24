cmake_minimum_required(VERSION 3.5)

# Указываем, что это подпроект
project(formatter LANGUAGES CXX VERSION 1.0)

# Проверяем, не был ли target уже добавлен
if(NOT TARGET formatter)
    add_library(formatter STATIC formatter.cpp)
    target_include_directories(formatter PUBLIC 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    )
endif()
