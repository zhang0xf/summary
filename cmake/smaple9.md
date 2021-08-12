## 生成安装包

配置生成各种平台上的安装包，包括二进制安装包和源码安装包。为了完成这个任务，我们需要用到 CPack ，它同样也是由 CMake 提供的一个工具，专门用于打包。
1. 导入 InstallRequiredSystemLibraries 模块，以便之后导入 CPack 模块；
2. 设置一些 CPack 相关变量，包括版权信息和版本信息，其中版本信息使用了上一案例定义的版本号；
3. 导入 CPack 模块。

## 目录结构

* CMakeLists.txt
* config.h.in
* License.txt
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
 14 set(main_VERSION_MAJOR "1")
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
102 
103 #创建一个CPack安装包
104 include(InstallRequiredSystemLibraries)
105 set(CPACK_RESOURCE_FILE_LICENSE
106     "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
107 message("${CPACK_RESOURCE_FILE_LICENSE}")
108 set(CPACK_PACKAGE_VERSION_MAJOR "${main_VERSION_MAJOR}")
109 set(CPACK_PACKAGE_VERSION_MINOR "${main_VERSION_MINOR}")
110 include(CPack)
```
1. [InstallRequiredSystemLibraries](https://cmake.org/cmake/help/v3.15/module/InstallRequiredSystemLibraries.html?highlight=installrequiredsystemlibraries) : Include this module to search for compiler-provided system runtime libraries and add install rules for them. Some optional variables may be set prior to including the module to adjust behavior:~
2. [CPACK_RESOURCE_FILE_LICENSE](https://cmake.org/cmake/help/v3.15/module/CPack.html?highlight=cpack_resource_file_license) : License to be embedded in the installer. It will typically be displayed to the user by the produced installer (often with an explicit “Accept” button, for graphical installers) prior to installation. This license file is NOT added to the installed files but is used by some CPack generators like NSIS. If you want to install a license file (may be the same as this one) along with your project, you must add an appropriate CMake [install()](https://cmake.org/cmake/help/v3.15/command/install.html#command:install) command in your CMakeLists.txt.
3. [CPACK_PACKAGE_VERSION_MAJOR](https://cmake.org/cmake/help/v3.15/module/CPack.html?highlight=cpack_package_version_major) : Package major version. This variable will always be set, but its default value depends on whether or not version details were given to the [project()](https://cmake.org/cmake/help/v3.15/command/project.html#command:project) command in the top level CMakeLists.txt file. If version details were given, the default value will be [CMAKE_PROJECT_VERSION_MAJOR](https://cmake.org/cmake/help/v3.15/variable/CMAKE_PROJECT_VERSION_MAJOR.html#variable:CMAKE_PROJECT_VERSION_MAJOR). If no version details were given, a default version of 0.1.1 will be assumed, leading to CPACK_PACKAGE_VERSION_MAJOR having a default value of 0.
4. [CPACK_PACKAGE_VERSION_MINOR](https://cmake.org/cmake/help/v3.15/module/CPack.html?highlight=cpack_package_version_minor) : Package minor version. The default value is determined based on whether or not version details were given to the [project()](https://cmake.org/cmake/help/v3.15/command/project.html#command:project) command in the top level CMakeLists.txt file. If version details were given, the default value will be [CMAKE_PROJECT_VERSION_MINOR](https://cmake.org/cmake/help/v3.15/variable/CMAKE_PROJECT_VERSION_MINOR.html#variable:CMAKE_PROJECT_VERSION_MINOR), but if no minor version component was specified then CPACK_PACKAGE_VERSION_MINOR will be left unset. If no project version was given at all, a default version of 0.1.1 will be assumed, leading to CPACK_PACKAGE_VERSION_MINOR having a default value of 1.
5. [CPack](https://cmake.org/cmake/help/v3.15/module/CPack.html?highlight=cpack) : Build binary and source package installers.

## LIsense.txt

```
版权申明之类的，随便复制一段话即可
```

## 构建工程并执行CPack

* 生成二进制安装包
```
cpack -C CPackConfig.cmake
```
* 生成源码安装包
```
cpack -C CPackSourceConfig.cmake
```
* 执行cpack命令后，会生成3个不同形式的二进制包。包的名称由设置好的项目名字和版本号组成
```
test3-1.0.1-Linux.sh
test3-1.0.1-Linux.tar.Z
test3-1.0.1-Linux.tar.gz
```
* ./test3-1.0.1-Linux.sh执行安装包
```
./test3-1.0.1-Linux.sh
```
```
test3 Installer Version: 1.0.1, Copyright (c) Humanity
This is a self-extracting archive.
The archive will be extracted to: /home/zhangf/cmake_exercise/md_build

If you want to stop extracting, please press <ctrl-C>.
T License (MIT)

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


Do you accept the license? [yN]: 
```
```
y
```
```
By default the test3 will be installed in:
  "/home/zhangf/cmake_exercise/md_build/test3-1.0.1-Linux"
Do you want to include the subdirectory test3-1.0.1-Linux?
Saying no will install in: "/home/zhangf/cmake_exercise/md_build" [Yn]: 
```
```
y
```
```
Using target directory: /home/zhangf/cmake_exercise/md_build/test3-1.0.1-Linux
Extracting, please wait...

Unpacking finished successfully
```
* 在当前目录下会多一个test3-1.0.1-Linux目录，执行二进制文件测试是否安装成功
```
./test3-1.0.1-Linux/bin/main 2 3
```
