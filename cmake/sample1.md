## 目录结构

* CMakeLists.txt
* main.cpp

## CMakeLists.txt
```
  1 #指定cmake的版本
  2 cmake_minimum_required(VERSION 2.8)
  3 
  4 #指定工程名称
  5 project(test1)
  6 
  7 #指定生成目标
  8 add_executable(main main.cpp)
```
1. [cmake_minimum_required](https://cmake.org/cmake/help/v3.15/command/cmake_minimum_required.html?highlight=cmake_minimum_required) : Require a minimum version of cmake.
2. [project](https://cmake.org/cmake/help/v3.15/command/project.html?highlight=project) : Set the name of the project.
3. [add_executable](https://cmake.org/cmake/help/v3.15/command/add_executable.html?highlight=add_executable) : Add an executable to the project using the specified source files.
