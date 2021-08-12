## 目录结构

* CMakeLists.txt
* main.cpp
* mymath
  * CMakeLists.txt
  * mymath.cpp
  * mymath.h
* README.md

## CMakeLists.txt
```
  1 #指定cmake版本号
  2 cmake_minimum_required(VERSION 2.8)
  3 
  4 #指定工程名称
  5 project(test3)
  6 
  7 #添加mymath子目录
  8 add_subdirectory(mymath)
  9 
 10 #获得当前目录下的所有源文件，并将名称保存到变量中
 11 aux_source_directory(. DIR_SRCS)
 12 
 13 #指定生成目标
 14 add_executable(main ${DIR_SRCS})
 15 
 16 #将mymath下源文件生成链接库提供给可执行程序
 17 target_link_libraries(main mymath)
```
1. [add_subdirectory](https://cmake.org/cmake/help/v3.15/command/add_subdirectory.html?highlight=add_subdirectory) : Add a subdirectory to the build.
2. [target_link_libraries](https://cmake.org/cmake/help/v3.15/command/target_link_libraries.html?highlight=target_link_libraries) : Specify libraries or flags to use when linking a given target and/or its dependents. Usage requirementsfrom linked library targets will be propagated. Usage requirements of a target’s dependencies affect compilation of its own sources.

## CMakeLists.txt
```
  1 #查找当前目录下所有源文件，并存放在变量中
  2 aux_source_directory(. DIR_SRCS)
  3 
  4 #将源文件生成静态库
  5 add_library(mymath ${DIR_SRCS})
```
1. [add_library](https://cmake.org/cmake/help/v3.15/command/add_library.html?highlight=add_library) : Add a library to the project using the specified source files.
