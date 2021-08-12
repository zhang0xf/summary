## 支持gdb

让 CMake 支持 gdb，只需要指定 Debug 模式下开启 -g 选项。

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
46 
47 # 指定安装路径
48 install (TARGETS main DESTINATION bin)
49 install (FILES "${PROJECT_BINARY_DIR}/config.h"
50          DESTINATION include)
51 
52 #启用测试
53 enable_testing()
54 
55 #测试程序是否成功运行
56 add_test(test_run main 2 3)
57 
58 #测试帮助信息是否可以正常显示
59 add_test(test_usage main)
60 set_tests_properties(test_usage
61     PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.* a b")
62 
63 #测试5和7的和
64 add_test(test_5_7 main 5 7)
65 set_tests_properties(test_5_7 
66     PROPERTIES PASS_REGULAR_EXPRESSION "is 12")
67 
68 #测试6和8的和
69 add_test(test_6_8 main 6 8)
70 set_tests_properties(test_6_8
71     PROPERTIES PASS_REGULAR_EXPRESSION "is 14")
72 
73 #编写宏，用来简化测试工作
74 macro(do_test arg1 arg2 result)
75     add_test(test_${arg1}_${arg2} main ${arg1} ${arg2})
76     set_tests_properties(test_${arg1}_${arg2}
77         PROPERTIES PASS_REGULAR_EXPRESSION ${result})
78 endmacro(do_test)
79 
80 do_test(1 2 "is 3")
81 do_test(3 5 "is 8")
82 do_test(10 20 "is 30")
83 
84 #加入gdb调试，只需在debug模式下开启-g选项
85 set(CMAKE_BUILD_TYPE "Debug")
86 set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
87 set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
```
1. [CMAKE_BUILD_TYPE](https://cmake.org/cmake/help/v3.15/variable/CMAKE_BUILD_TYPE.html?highlight=cmake_build_type) : Specifies the build type on single-configuration generators.
2. [CXXFLAGS](https://cmake.org/cmake/help/v3.15/envvar/CXXFLAGS.html?highlight=cxxflags) : This is a CMake Environment Variable. Its initial value is taken from the calling process environment.
3. [CMAKE_CXX_FLAGS_DEBUG / CMAKE_CXX_FLAGS_RELEASE](https://cmake.org/pipermail/cmake/2006-February/008371.html) : **CMAKE_CXX_FLAGS_RELEASE** and **CMAKE_CXX_FLAGS_DEBUG** are appended to  **CMAKE_CXX_FLAGS** if the buildtype is release or debug.
4. [-ggdb](https://www.jianshu.com/p/55207232c00d) : 使GCC为GDB生成专用的更为丰富的调试信息；但是，此时就不能用其他的调试器来进行调试了 (如 ddx).
