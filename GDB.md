# gdb

GDB的高频操作

## 生成调试信息

在编译时，使用 ***-g*** 参数把调试信息加到可执行文件中：
```
gcc -g hello.c -o hello
````
```
g++ -g hello.cpp -o hello
```

## 启动GDB

启动gdb
```
gdb program
```
设置参数
```
set args 10 20 30 40 50 
```
查看参数设置
```
show args
```
启动程序
```
run
```
查看当前工作目录
```
pwd
```

## 断点

在程序第10行设置断点
```
b 10
```
在函数 func 入口处设置断点
```
b func
```
在源文件 filename 的 linenum 行处停住
```
break test.c:20
```
在源文件 filename 的 function 函数的入口处停住
```
break test.c:func
```
在 类class 的 function 函数的入口处停住
```
break class::func
```
在命名空间 namespace 的 类class 的 function 函数的入口处停住
```
break namespace::class::func
```
设置条件断点，使用 if 关键词 + 断点条件
```
b test.c:8 if intValue == 5
```
查询断点信息
```
info b
```
delete 删除断点（若不指定range，表示删除所有断点）
```
d [range...]
```
disable 失效断点（若不指定range，表示失效所有断点，并不会删除断点）
```
dis [range...]
```
enable 激活断点（若不指定range，表示激活所有断点）
```
ena [range...]
```

## 调试

run 运行程序
```
r
```
next 单步跟踪，不进入函数内部
```
n
```
step 单步跟踪，进入函数内部
```
s
```
finish 退出进入的函数
```
finish
```
until 可以运行程序直到退出当前循环体
```
u
```
continue 继续运行程序
```
c
```

## 打印变量

print 手动打印变量值
```
p val
```
设置变量自动显示
```
display val
```
查看display设置信息
```
info display
```
隐藏 info display 时显示的编号
```
undisplay num
```
删除自动显示，dnums为编号，以空格分隔，支持范围删除（2-5）
```
delete display dnums…
```
disable 失效自动显示， dnums为编号，不会删除自动显示设置
```
disable display dnums…
```
able 激活自动显示
```
enable display dnums…
```
ptype 查看变量 width 的类型
```
ptype width
```
set 改变变量值
```
set var width=47
```

## 显示源码

list显示源码，默认打印10行（上下文各5行）
```
list linenum
```
显示function函数的源码，默认打印10行（上下文各是2行、8行）
```
list function
```
显示当前行 后面 的源程序
```
list
```
显示当前行 前面 的源程序
```
list -
```
set listsize 设置显示源码的行数
```
set listsize count
```
show listsize 查看当前listsize设置
```
show listsize
```

## 多线程

显示当前可调试的所有线程，会显示线程ID
```
info threads 
```
切换到指定ID的线程
```
thread ID 
```
在所有线程的某行设置断点
```
break file.c:123 thread all
```
在一系列线程上执行命令
```
thread apply [thread-id-list] [all] args
```
控制打印线程启动或结束是的信息
```
set print thread-events
```
使用step或是continue调试时，其他线程会并行执行，需锁定线程
```
set scheduler-locking off|on|step
```
`off：不锁定任何线程，也就是所有的线程都执行，这是默认值。`

`on：只有当前被调试的线程能够执行。`

`step：阻止其他线程在当前线程单步调试时，抢占当前线程。只有当next、continue、util以及finish的时候，其他线程才会获得重新运行的机会。`

## .core文件

查看是否可以生成.core文件
```
ulimit -c
```
允许生成.core文件且不限制大小
```
ulimit -c unlimited
```
GDB调试.core文件：`gdb ./main main.core`
```
gdb [exec file] [core file]
```
查找段错误位置
```
where
```



