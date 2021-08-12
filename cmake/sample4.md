## 自定义编译选项
CMake 允许为项目增加编译选项，从而根据用户的环境和需求选择最合适的编译方案。例如：可以将 mymath 设置为可选库。如果选项为 ON ，就使用该库定义的数学函数，否则就使用标准库中的数学函数库。

###### 目录结构

* CMakeLists.txt
* config.h.in
* main.cpp
* mymath
  * CMakeLists.txt
  * mymath.cpp
  * mymath.h

## CMakeLists.txt
```
  1 #指定cmake版本号
  2 cmake_minimum_required(VERSION 2.8)
  3 
  4 #指定工程名称
  5 project(test4)
  6 
  7 #加入一个配置头文件，用于处理cmake对源码的设置
  8 #configure_file(
  9 #   "${PROJECT_SOURCE_DIR}/config.h.in"
 10 #   "${PROJECT_BINARY_DIR}/config.h"
 11 #   )
 12 
 13 message("${PROJECT_SOURCE_DIR}")
 14 message("${PROJECT_BINARY_DIR}")
 15 
 16 #添加选项USE_MYMATH，默认值是ON
 17 option(USE_MYMATH "use provided math implementation" ON)
 18 
 19 #加入一个配置头文件，用于处理cmake对源码的设置
 20 configure_file(
 21     "${PROJECT_SOURCE_DIR}/config.h.in"
 22     "${PROJECT_BINARY_DIR}/config.h"
 23     )
 24 
 25 if(USE_MYMATH)
 26     message("option USE_MYMATH is ${USE_MYMATH}")
 27 #else
 28 #   message("option USE_MYMATH is OFF")
 29 endif(USE_MYMATH)
 30 
 31 #是否加入mymath库
 32 if(USE_MYMATH)
 33     include_directories("${PROJECT_SOURCE_DIR}/mymath")
 34     add_subdirectory(mymath)
 35     set(EXTRA_LIBS ${EXTRA_LIBS} mymath)
 36 endif(USE_MYMATH)
 37 
 38 #获得当前目录下的所有源文件，并将名称保存到变量中
 39 aux_source_directory(. DIR_SRCS)
 40 
 41 #指定生成目标
 42 add_executable(main ${DIR_SRCS})
 43 
 44 #将mymath下的作为链接库提供给可执行程序
 45 target_link_libraries(main ${EXTRA_LIBS})
```
1. [message](https://cmake.org/cmake/help/v3.15/command/message.html?highlight=message) : Display a message to the user.
2. [option](https://cmake.org/cmake/help/v3.15/command/option.html?highlight=option) : Provide an option that the user can optionally select.
3. [configure_file](https://cmake.org/cmake/help/v3.15/command/configure_file.html?highlight=configure_file) : Copy a file to another location and modify its contents. 
4. [PROJECT_SOURCE_DIR](https://cmake.org/cmake/help/v3.15/variable/PROJECT_SOURCE_DIR.html?highlight=project_source_dir) : Top level source directory for the current project.
5. [PROJECT_BINARY_DIR](https://cmake.org/cmake/help/v3.15/variable/PROJECT_BINARY_DIR.html?highlight=project_binary_dir) : Full path to build directory for project.
6. [include_directories](https://cmake.org/cmake/help/v3.15/command/include_directories.html?highlight=include_directories) : Add include directories to the build.
7. [set](https://cmake.org/cmake/help/v3.15/command/set.html?highlight=set) : Set a normal, cache, or environment variable to a given value. See the [cmake-language(7) variables](https://cmake.org/cmake/help/v3.15/manual/cmake-language.7.html#cmake-language-variables) documentation for the scopes and interaction of normal variables and cache entries.Signatures of this command that specify a <value>... placeholder expect zero or more arguments. Multiple arguments will be joined as a [semicolon-separated list](https://cmake.org/cmake/help/v3.15/manual/cmake-language.7.html#cmake-language-lists) to form the actual variable value to be set. Zero arguments will cause normal variables to be unset. See the unset() command to unset variables explicitly.

## main.cpp
根据宏 USE_MYMATH 进行条件编译，宏 USE_MYMATH 由 CMakeLists.txt 来生成。
```
 1 #include <stdlib.h>
 2 #include <stdio.h>
 3 #include <iostream>
 4 #include "../md_build/config.h"
 5 
 6 #ifdef USE_MYMATH
 7 #include "./mymath/mymath.h"
 8 #else
 9 #include <math.h>
10 #endif
11 
12 using namespace std;
13 
14 int main(int argc, char* argv[])
15 {
16     if(argc < 3)
17     {
18         printf("Usage:%s a b\n", argv[0]);
19         return 1;
20     }
21     int ret = 0;
22 
23     int a = atoi(argv[1]);
24     int b = atoi(argv[2]);
25 
26 #ifdef USE_MYMATH
27     printf("use ourself library\n");
28     ret = myAdd(a, b);
29     printf("a + b is %d\n", ret);
30 #else
31     printf("use system library\n");
32 #endif
33 
34     return 0;
35 }
```

## config.h.in
main.cpp 包含头文件 config.h。config.h 定义了 USE_MYMATH，我们编写 config.h.in 文件来传递配置，Cmake会根据 CMakeLists.txt 来自动生成 config.h。
```
 1 #cmakedefine USE_MYMATH
```

## 编译项目时的两种方式
                   
* 直接使用默认值。
* 使用**ccmake**或**cmake -i**交互式的选择选项的值。
![image text](https://github.com/zhang0xf/memo/blob/main/cmake/image/Image1.png)
![image text](https://github.com/zhang0xf/memo/blob/main/cmake/image/Image2.png)
![image text](https://github.com/zhang0xf/memo/blob/main/cmake/image/Image3.png)
