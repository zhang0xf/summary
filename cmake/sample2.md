## 目录结构

* CMakeLists.txt
* main.cpp
* mymath.cpp
* mymath.h

## CMakeLists.txt
```
  1 #指定cmake版本号
  2 cmake_minimum_required(VERSION 2.8)
  3 
  4 #指定工程名称
  5 project(test2)
  6 
  7 #指定生成目标
  8 #add_executable(main main.cpp mymath.cpp)
  9 
 10 #如果源文件过多，应使用变量存储
 11 aux_source_directory(. DIR_SRCS)
 12 
 13 #指定生成目标
 14 add_executable(main ${DIR_SRCS})
```
1. [aux_source_directory](https://cmake.org/cmake/help/v3.15/command/aux_source_directory.html?highlight=aux_source_directory) : Find all source files in a directory.
