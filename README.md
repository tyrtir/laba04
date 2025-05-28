# Лабораторная работа 4:
Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса Travis CI

[Ссылка на репозиторий GitHub](https://github.com/tyrtir/laba04)

## Выполнение работы

### 1. Подготовка проекта

Создана структура проекта:
lab04/<br>
├── .gitignore<br>
├── .github/<br>
│   └── workflows/<br>
│       └── main.yml<br>
├── CMakeLists.txt<br>
├── formatter_lib/<br>
│   ├── CMakeLists.txt<br>
│   ├── formatter.h<br>
│   └── formatter.cpp<br>
├── formatter_ex_lib/<br>
│   ├── CMakeLists.txt<br>
│   ├── formatter_ex.h<br>
│   └── formatter_ex.cpp<br>
├── solver_lib/<br>
│   ├── CMakeLists.txt<br>
│   ├── solver.h<br>
│   └── solver.cpp<br>
├── hello_world_application/<br>
│   ├── CMakeLists.txt<br>
│   └── hello_world.cpp<br>
└── solver_application/<br>
    ├── CMakeLists.txt<br>
    └── equation.cpp<br>
  
## Создаем .gitignore для отсеивания лишних файлов
.gitignore 
```
build/
*/build/
*.a
*.so
*.o
*.out
*.exe
*~
*.swp
.DS_Store
.vscode/
.idea/
```
## Создаем .github/workflows/main.yml:
```
name: CMake Build and Test

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

env:
  BUILD_TYPE: Release

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2

    - name: Install dependencies
      run: |
        sudo apt-get update
        sudo apt-get install -y build-essential cmake

    - name: Create Build Directory
      run: mkdir -p build

    - name: Configure CMake
      working-directory: ./build
      run: cmake .. -DCMAKE_BUILD_TYPE=${{ env.BUILD_TYPE }}

    - name: Build
      working-directory: ./build
      run: cmake --build . --config ${{ env.BUILD_TYPE }}

    - name: Run Hello World Test
      working-directory: ./build
      run: |
        ./hello_world_application/hello_world | grep -q "hello, world!"
        echo "Hello World test passed!"

    - name: Run Solver Test
      working-directory: ./build
      run: |
        echo "Testing equation solver:"
        OUTPUT=$(./solver_application/solver <<< "1 0 -25")
        echo "$OUTPUT"
        echo "$OUTPUT" | grep -q "x1 = -5.00000" || (echo "x1 test failed"; exit 1)
        echo "$OUTPUT" | grep -q "x2 = 5.00000" || (echo "x2 test failed"; exit 1)
        echo "Solver test passed!"
```
В результате получаем проверку всех тестов 
