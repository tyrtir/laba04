# Лабораторная работа 3: Сборка проекта с помощью CMake

[Ссылка на репозиторий GitHub](https://github.com/tyrtir/laba03)

## Цель работы
Освоить основы системы сборки CMake для многофайловых C++ проектов.

## Выполнение работы

### 1. Подготовка проекта

Создана структура проекта:
lab03/<br>
├── CMakeLists.txt # Главный файл конфигурации<br>
├── formatter_lib/ # Библиотека formatter<br>
│ ├── CMakeLists.txt<br>
│ ├── formatter.h<br>
│ └── formatter.cpp<br>
├── formatter_ex_lib/ # Расширенная библиотека<br>
│ ├── CMakeLists.txt<br>
│ ├── formatter_ex.h<br>
│ └── formatter_ex.cpp<br>
├── solver_lib/ # Библиотека для решения уравнений<br>
│ ├── CMakeLists.txt<br>
│ ├── solver.h<br>
│ └── solver.cpp<br>
├── hello_world_application/ # Пример приложения<br>
│ ├── CMakeLists.txt<br>
│ └── hello_world.cpp<br>
└── solver_application/ # Приложение-решатель<br>
  ├── CMakeLists.txt<br>
  └── equation.cpp<br>
### 2. Создание CMake файлов

CmakeLists.txt в formater_lib:
```
cat > formatter_lib/CMakeLists.txt <<EOF
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
EOF
```
Тут приведён усовершенствованный код, с проверкой существования target. Следующие CMake файлы будут также усовершенствованы, чтобы не возникало ошибок при повторной команде ```cmake ..```

CmakeLists.txt в formater_ex_lib:
```
cat > formatter_ex_lib/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.5)
project(formatter_ex LANGUAGES CXX)

if(NOT TARGET formatter)
    add_subdirectory(${CMAKE_SOURCE_DIR}/formatter_lib ${CMAKE_CURRENT_BINARY_DIR}/formatter_lib)
endif()

# Проверяем, не был ли target уже добавлен
if(NOT TARGET formatter_ex)
    add_library(formatter_ex STATIC formatter_ex.cpp)
    target_include_directories(formatter_ex PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    )
    target_link_libraries(formatter_ex PUBLIC formatter)
endif()
EOF
```

CMakeLists.txt в hello_world_application:
```
cat > hello_world_application/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.5)

project(hello_world LANGUAGES CXX)

# Подключаем formatter_ex_lib
if(NOT TARGET formatter_ex)
    add_subdirectory(${CMAKE_SOURCE_DIR}/formatter_ex_lib 
                   ${CMAKE_CURRENT_BINARY_DIR}/formatter_ex_lib)
endif()

add_executable(hello_world hello_world.cpp)
target_link_libraries(hello_world PRIVATE formatter_ex)
EOF
```

CMakeLists.txt в solver_application:
```
cat > solver_application/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.5)
project(solver_app LANGUAGES CXX)

# Подключаем необходимые библиотеки
if(NOT TARGET formatter_ex)
    add_subdirectory(${CMAKE_SOURCE_DIR}/formatter_ex_lib 
                   ${CMAKE_CURRENT_BINARY_DIR}/formatter_ex_lib)
endif()

if(NOT TARGET solver_lib)
    add_subdirectory(${CMAKE_SOURCE_DIR}/solver_lib 
                   ${CMAKE_CURRENT_BINARY_DIR}/solver_lib)
endif()

add_executable(solver equation.cpp)
target_link_libraries(solver PRIVATE formatter_ex solver_lib)
EOF
```

CMakeLists.txt в solver_lib:
```
cat > solver_lib/CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.5)
project(solver_lib LANGUAGES CXX)

# Проверяем, не был ли target уже добавлен
if(NOT TARGET solver_lib)
    add_library(solver_lib STATIC solver.cpp)
    target_include_directories(solver_lib PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    )
endif()
EOF
```

CMakeLists.txt в корневой папке проекта:
```
cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.5)
project(lab03 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Включаем необходимые политики
cmake_policy(SET CMP0002 NEW)
cmake_policy(SET CMP0079 NEW)

# Подключаем подпроекты в правильном порядке
add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)  # Добавляем solver_lib
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)
EOF
```

### 3. Основные команды

Создание директории для сборки
```bash
mkdir build && cd build
```

Генерация Makefile (из корня проекта)
```bash
cmake ..
```
Вывод:
```bash
-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vm/maxonhick/workspace/projects/Homework_Lab03/build
```

Сборка всех компонентов
```bash
cmake --build .
```
Вывод:
```bash
[ 20%] Built target formatter
[ 40%] Built target formatter_ex
[ 60%] Built target solver_lib
[ 80%] Built target hello_world
[100%] Built target solver
```

# Запуск примеров
```
└─▪ ./hello_world_application/hello_world 
-------------------------
hello, world!
-------------------------
└─▪ ./solver_application/solver 
1 0 -25
-------------------------
x1 = -5.000000
-------------------------
-------------------------
x2 = 5.000000
-------------------------
```
Тут приведён пример решения уравнения x^2 - 25 = 0
