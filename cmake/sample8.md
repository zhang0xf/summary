## 添加版本号

给项目添加和维护版本号是一个好习惯，这样有利于用户了解每个版本的维护情况，并及时了解当前所用的版本是否过时，或是否可能出现不兼容的情况。


## 目录结构

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
  5 project(test3)
  6 
  7 #加入一个配置头文件，用于处理cmake对源码的设置
  8 #configure_file(
  9 #   "${PROJECT_SOURCE_DIR}/config.h.in"
 10 #   "${PROJECT_BINARY_DIR}/config.h"
 11 #   )
 12 
 13 #分别指定项目的主版本号和副版本号
 14 set(main_VERSION_MAJOR 1)
 15 set(main_VERSION_MINOR 0)
 16 
 17 message("${PROJECT_SOURCE_DIR}")
 18 message("${PROJECT_BINARY_DIR}")
 19 
 20 #是否使用自己的mymath库
 21 option(USE_MYMATH "use provided math implementation" ON)
 22 
 23 #检查系统是否支持pow函数
 24 include(${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
 25 check_function_exists(pow HAVE_POW)
 26 
 27 message("${HAVE_POW}")
 28 if(HAVE_POW)
 29     message("we are arriving here for debugging.")
 30     message("macro HAVE_POW is ${HAVE_POW}")
 31 endif(HAVE_POW)
 32 
 33 #加入一个配置头文件，用于处理cmake对源码的设置
 34 configure_file(
 35     "${PROJECT_SOURCE_DIR}/config.h.in"
 36     "${PROJECT_BINARY_DIR}/config.h"
 37     )
 38 
 39 if(USE_MYMATH)
 40     message("option USE_MYMATH is ${USE_MYMATH}")
 41 #else
 42 #   message("option USE_MYMATH is OFF")
 43 endif(USE_MYMATH)
 44 
 45 #是否加入mymath库
 46 if(USE_MYMATH)
 47     include_directories("${PROJECT_SOURCE_DIR}/mymath")
 48     add_subdirectory(mymath)
 49     set(EXTRA_LIBS ${EXTRA_LIBS} mymath)
 50 endif(USE_MYMATH)
 51 
 52 #获得当前目录下的所有源文件，并将名称保存到变量中
 53 aux_source_directory(. DIR_SRCS)
 54 
 55 #指定生成目标
 56 add_executable(main ${DIR_SRCS})
 57 
 58 #将mymath下的作为链接库提供给可执行程序
 59 target_link_libraries(main ${EXTRA_LIBS})
 60 
 61 # 指定安装路径
 62 install (TARGETS main DESTINATION bin)
 63 install (FILES "${PROJECT_BINARY_DIR}/config.h"
 64          DESTINATION include)
 65 
 66 #启用测试
 67 enable_testing()
 68 
 69 #测试程序是否成功运行
 70 add_test(test_run main 2 3)
 71 
 72 #测试帮助信息是否可以正常显示
 73 add_test(test_usage main)
 74 set_tests_properties(test_usage
 75     PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.* a b")
 76 
 77 #测试5和7的和
 78 add_test(test_5_7 main 5 7)
 79 set_tests_properties(test_5_7 
 80     PROPERTIES PASS_REGULAR_EXPRESSION "is 12")
 81 
 82 #测试6和8的和
 83 add_test(test_6_8 main 6 8)
 84 set_tests_properties(test_6_8
 85     PROPERTIES PASS_REGULAR_EXPRESSION "is 14")
 86 
 87 #编写宏，用来简化测试工作
 88 macro(do_test arg1 arg2 result)
 89     add_test(test_${arg1}_${arg2} main ${arg1} ${arg2})
 90     set_tests_properties(test_${arg1}_${arg2}
 91         PROPERTIES PASS_REGULAR_EXPRESSION ${result})
 92 endmacro(do_test)
 93 
 94 do_test(1 2 "is 3")
 95 do_test(3 5 "is 8")
 96 do_test(10 20 "is 30")
 97 
 98 #加入gdb调试，只需在debug模式下开启-g选项
 99 set(CMAKE_BUILD_TYPE "Debug")
100 set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
101 set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
```
1. [set](https://cmake.org/cmake/help/latest/command/set.html) : Set a normal, cache, or environment variable to a given value.

###### config.h.in
```
  1 #cmakedefine USE_MYMATH
  2 #cmakedefine HAVE_POW
  3 
  4 #define main_VERSION_MAJOR_TEST @main_VERSION_MAJOR@
  5 #define main_VERSION_MINOR_TEST @main_VERSION_MINOR@
```
1. #cmakedefine 和 #define 的区别：cmakedefine的宏不一定存在，define的宏一定存在，但值不确定。

###### main.cpp
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
 18         // print version info
 19         printf("%s version %d.%d\n", argv[0], main_VERSION_MAJOR_TEST, main_VERSION_MINOR_TEST);
 20         printf("Usage:%s a b\n", argv[0]);
 21         return 1;
 22     }
 23     int ret = 0;
 24 
 25     int a = atoi(argv[1]);
 26     int b = atoi(argv[2]);
 27 
 28 #ifdef USE_MYMATH
 29     printf("use ourself library\n");
 30     ret = myAdd(a, b);
 31     printf("a + b is %d\n", ret);
 32 #else
 33     printf("use system library\n");
 34 #endif
 35 
 36 #ifdef HAVE_POW
 37     printf("use standard lib : pow\n");
 38 #else
 39     printf("use ourself lib : mypow\n");
 40 #endif
 41 
 42     return 0;
 43 }
```
