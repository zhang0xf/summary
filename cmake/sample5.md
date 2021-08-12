## 安装和测试
CMake 可以指定安装规则及添加测试。这两个功能分别通过产生 Makefile 后使用 make install 和 make test 来实现。在以前的 GNU Makefile 里，你可能需要为此编写 install 和 test 两个伪目标和相应的规则，但在 CMake 里，这样的工作只需要简单的几条命令。

## 安装

1. 使用 Install 命令实现安装。
2. main 和 libmymath.a 会被复制到 **/usr/local/bin** 。
3. mymath.h 和 config.h 会被复制到 **/usr/local/include**。
4. **/usr/local/** 是默认安装到的根目录，使用变量 **CMAKE_INSTALL_PREFIX** 来指定根目录。

### 目录结构

* CMakeLists.txt
* config.h.in
* main.cpp
* mymath
  * CMakeLists.txt
  * mymath.cpp
  * mymath.h

### CMakeLists.txt
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
```

### CMakeLists.txt
```
  1 #查找当前目录下所有源文件，并存放在变量中
  2 aux_source_directory(. DIR_SRCS)
  3 
  4 #生成链接库
  5 add_library(mymath ${DIR_SRCS})
  6 
  7 # 指定 MathFunctions 库的安装路径
  8 install (TARGETS mymath DESTINATION bin)
  9 install (FILES mymath.h DESTINATION include)
```
1. [install](https://cmake.org/cmake/help/v3.15/command/install.html?highlight=install) : Specify rules to run at install time.

## 测试

CMake 提供了一个称为 CTest 的测试工具。我们在项目根目录的 CMakeLists.txt 中调用 add_test 命令。

### CMakeLists.txt
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
```
1. [enable_testing](https://cmake.org/cmake/help/v3.15/command/enable_testing.html?highlight=enable_testing) : Enable testing for current directory and below.
2. [add_test](https://cmake.org/cmake/help/v3.15/command/add_test.html?highlight=add_test) : Add a test to the project to be run by [ctest(1)](https://cmake.org/cmake/help/v3.15/manual/ctest.1.html#manual:ctest(1)).
3. [set_tests_properties](https://cmake.org/cmake/help/v3.15/command/set_tests_properties.html?highlight=set_tests_properties) : Set a property of the tests.
4. [PASS_REGULAR_EXPRESSION](https://cmake.org/cmake/help/v3.15/prop_test/PASS_REGULAR_EXPRESSION.html?highlight=pass_regular_expression) : The output must match this regular expression for the test to pass.If set, the test output will be checked against the specified regular expressions and at least one of the regular expressions has to match, otherwise the test will fail. **PASS_REGULAR_EXPRESSION** expects a list of regular expressions. ""中的匹配格式要严格与main.cpp中的printf输出格式严格匹配而不是return返回值，至于为什么尚未可知。
5. [macro](https://cmake.org/cmake/help/v3.15/command/macro.html?highlight=macro) : Start recording a macro for later invocation as a command. 编写宏来简化测试工作。
