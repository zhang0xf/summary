# makefile

## 编写Makefile生成可执行文件
```
  1 #指定编译器选项
  2 CC = gcc
  3 WORKDIR = .
  4 CFLAGS = -Wall -g
  5 INCFLAG = -I$(WORKDIR)/inc
  6 SRCDIR = $(WORKDIR)/src
  7 LIBFLAG = -L$(WORKDIR)/../lib
  8 
  9 SRCS = $(wildcard $(SRCDIR)/*.c)
 10 
 11 NODIR = $(notdir $(SRCS))
 12 
 13 OBJS = $(patsubst %.c, %.o, $(NODIR))
 14 
 15 BIN = client
 16 
 17 $(BIN):$(OBJS)
 18     $(CC) $(LIBFLAG) $^ -o $@ -lsocket
 19     mv $(WORKDIR)/*.o $(SRCDIR)
 20 
 21 %.o:$(SRCDIR)/%.c
 22     $(CC) $(CFLAGS) $(INCFLAG) -c $< -o $@
 23 
 24 .PHONY:clean
 25 
 26 clean:
 27     rm $(WORKDIR)/$(BIN) $(SRCDIR)/*.o
```

* **wildcard**：扩展通配符，把指定目录下的所有.c文件全部展开，包含文件路径。
* **notdir**：去除路径信息。
* **patsubst**：把指定变量符合后缀的.c全部换成.o。
* **$^**: 所有的依赖文件。
* **$@**: 目标文件。
* **$<**: 第一个依赖文件。
* **%.o : %.c**：所有的.o目标文件，都自动来找这条通配规则生成 。
* **.PHONY**：表示clean是一个伪目标，即makefile不会把clean当作一个目标文件去处理。
* **rm命令**： makefile中也可以执行shell的命令，包括rm、cp、mv等等。 
 
 ##### 编写Makefile生成动态库
 
 **动态库路径**：可以是相对路径。
 
 **动态库名称**：掐头去尾。如：“libsocket.so”的动态库名称为socket。参数形式为 -lsocket。

## 编写Makefile生成动态库
```
 # 指定编译器选项
  2 CC = gcc
  3 WORKDIR = .
  4 VPATH = $(WORKDIR)/src
  5 CFLAGS = -Wall -g
  6 INCFLAGS = -I$(WORKDIR)/inc
  7 TARGETDIR = ../../lib
  8 
  9 OBJS = socketutil.o poolsocket.o socketlog.o
 10 LIB = libsocket.so
 11 
 12 $(LIB):$(OBJS)
 13     $(CC) -shared -fPIC $^ -o $@
 14     cp $(LIB) $(TARGETDIR)/$(LIB)
 15     mv $(WORKDIR)/*.o $(VPATH)
 16 
 17 .c.o:
 18     $(CC) $(CFLAGS) $(INCFLAGS) -c $< -o $@ -shared -fPIC
 19 
 20 .PHONY:clean
 21 
 22 clean:
 23     rm $(WORKDIR)/$(LIB) $(VPATH)/*.o $(TARGETDIR)/$(LIB) -rf
```

* **shared参数**：表示要生成动态库
* **-fPIC参数**： 表示使用地址无关技术
* **.c.o通配符**：相当于%.c : %.o
* **生成.o文件时的-shared -fPIC参数**：必须要加，否则链接时提示.o不符合要求。

## 程序运行时找不到动态库

* **临时解决方案**：export LD_LIBRARY_PATH=/动态库绝对路径；
* **永久解决方案**：将环境变量写入配置文件~/.bashrc 等。

## 查看动态库中的函数接口

**nm命令**: 动态库为libsocket.so，则使用nm libsocket.so来查看。
