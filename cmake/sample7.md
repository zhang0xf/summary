## 添加环境检查

有时候可能要对系统环境做点检查，例如要使用一个平台相关的特性的时候。在这个例子中，我们检查系统是否自带 pow 函数。如果有 pow 函数就使用它；否则使用我们定义的 power 函数。

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
13 message("${PROJECT_SOURCE_DIR}")
14 message("${PROJECT_BINARY_DIR}")
15 
16 #是否使用自己的mymath库
17 option(USE_MYMATH "use provided math implementation" ON)
18 
19 #检查系统是否支持pow函数
20 include(${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
21 check_function_exists(pow HAVE_POW)
22
23 message("${HAVE_POW}")
24 if(HAVE_POW)
25     message("we are arriving here for debugging.")
26     message("macro HAVE_POW is ${HAVE_POW}")
27 endif(HAVE_POW)
28 
29 #加入一个配置头文件，用于处理cmake对源码的设置
30 configure_file(
31     "${PROJECT_SOURCE_DIR}/config.h.in"
32     "${PROJECT_BINARY_DIR}/config.h"
33     )
34 
35 if(USE_MYMATH)
36     message("option USE_MYMATH is ${USE_MYMATH}")
37 #else
38 #   message("option USE_MYMATH is OFF")
39 endif(USE_MYMATH)
40 
41 #是否加入mymath库
42 if(USE_MYMATH)
43     include_directories("${PROJECT_SOURCE_DIR}/mymath")
44     add_subdirectory(mymath)
45     set(EXTRA_LIBS ${EXTRA_LIBS} mymath)
46 endif(USE_MYMATH)
47 
48 #获得当前目录下的所有源文件，并将名称保存到变量中
49 aux_source_directory(. DIR_SRCS)
50 
51 #指定生成目标
52 add_executable(main ${DIR_SRCS})
53 
54 #将mymath下的作为链接库提供给可执行程序
55 target_link_libraries(main ${EXTRA_LIBS})
56 
57 # 指定安装路径
58 install (TARGETS main DESTINATION bin)
59 install (FILES "${PROJECT_BINARY_DIR}/config.h"
60          DESTINATION include)
61 
62 #启用测试
63 enable_testing()
64 
65 #测试程序是否成功运行
66 add_test(test_run main 2 3)
67 
68 #测试帮助信息是否可以正常显示
69 add_test(test_usage main)
70 set_tests_properties(test_usage
71     PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.* a b")
72 
73 #测试5和7的和
74 add_test(test_5_7 main 5 7)
75 set_tests_properties(test_5_7 
76     PROPERTIES PASS_REGULAR_EXPRESSION "is 12")
77 
78 #测试6和8的和
79 add_test(test_6_8 main 6 8)
80 set_tests_properties(test_6_8
81     PROPERTIES PASS_REGULAR_EXPRESSION "is 14")
82 
83 #编写宏，用来简化测试工作
84 macro(do_test arg1 arg2 result)
85     add_test(test_${arg1}_${arg2} main ${arg1} ${arg2})
86     set_tests_properties(test_${arg1}_${arg2}
87         PROPERTIES PASS_REGULAR_EXPRESSION ${result})
88 endmacro(do_test)
89 
90 do_test(1 2 "is 3")
91 do_test(3 5 "is 8")
92 do_test(10 20 "is 30")
93 
94 #加入gdb调试，只需在debug模式下开启-g选项
95 set(CMAKE_BUILD_TYPE "Debug")
96 set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
97 set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
```

## config.h.in
```
  1 #cmakedefine USE_MYMATH
  2 #cmakedefine HAVE_POW
```

## main.cpp
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
 34 #ifdef HAVE_POW
 35     printf("use standard lib : pow\n");
 36 #else
 37     printf("use ourself lib : mypow\n");
 38 #endif
 39     
 40     return 0;
 41 }
```
1. [CMAKE_ROOT](https://cmake.org/cmake/help/v3.15/variable/CMAKE_ROOT.html?highlight=cmake_root) : Install directory for running cmake. This is the install root for the running CMake and the Modules directory can be found here. This is commonly used in this format: ${CMAKE_ROOT}/Modules
2. [CheckFunctionExists](https://cmake.org/cmake/help/v3.15/module/CheckFunctionExists.html?highlight=checkfunctionexists) : Check if a C function can be linked
