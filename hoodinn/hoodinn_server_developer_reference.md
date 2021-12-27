# go语言规范

https://golang.org/doc/effective_go.html

https://code.google.com/p/go-wiki/wiki/CodeReviewComments

https://docs.huihoo.com/go/golang.org/doc/effective_go.html

https://www.zhihu.com/question/347872912/answer/837345346

 

# go语言学习

https://segmentfault.com/a/1190000017890174 [Golang并发：再也不愁选channel还是选锁](https://segmentfault.com/a/1190000017890174)

 

 

# go gotchas

http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang/

https://blog.csdn.net/gongpulin/article/details/82956111 （中文版）

 

# linux开发环境搭建



golang 安装

vscode安装配置参考链接：https://www.jianshu.com/p/febbf1e975b6

redis安装配置参考链接：https://jingyan.baidu.com/article/a681b0de5755d73b184346e9.html

mysql安装配置参考链接：https://jingyan.baidu.com/article/20b68a88e0dfad796dec6243.html



**protobuf 3.5.1** 安装包：https://github.com/protocolbuffers/protobuf/blob/master/src/README.md

protobuf 3.5.1 安装教程参考：https://blog.csdn.net/libaineu2004/article/details/80706083

安装过程中出现./autogen.sh: 34: ./autogen.sh: curl: not found 的错误提示，键入apt-get install curl 即可

**gogoprotobuf**安装    github.com/gogo/protobuf



qq 应用商店安装

weixin 应用商店安装



zsh 安装参考链接：https://blog.csdn.net/amoscykl/article/details/80616873

zsh插件配置参考链接：https://blog.csdn.net/llmmll08/article/details/80687274



# 技术分享



[如何在技术团队中推行OKR](https://yq.aliyun.com/articles/184019)

[What is OKR? A goal-setting framework for thinking big](https://www.cio.com/article/3302036/okr-objectives-and-key-results-defined.html)

[Full Cycle Developers at Netflix](https://medium.com/netflix-techblog/full-cycle-developers-at-netflix-a08c31f83249)

[Software Engineering at Google](http://192.168.5.141:8080/xwiki/bin/view/技术分享/Software Engineering at Google/)

[Go Unit Tests: Tips from the Trenches](https://www.red-gate.com/simple-talk/dotnet/software-testing/go-unit-tests-tips-from-the-trenches/)

# git 使用规范

hoodinn的git规范以 Git Flow 工作流的规范为主，做了微调，以release分支替代了master分支。原文链接https://nvie.com/posts/a-successful-git-branching-model/

#### **1.**git分支管理摘要

**1.1** 主线分支

主线分支包括2个主分支，为两个长期分支。包括:

1.1.1 release。该分支最新代码是版本可发布状态，相当于传统的master分支。该分支通常从develop分支派生。当develop功能开发完成，并测试之后，可以派生release分支，做为版本预发布分支。测试基于该分支做待上线发布版本的测试，并在release分支上修复bug。测试通过后，将release分支合并到develop分支，同时打版本tag，标记为线上发布版本。

1.1.2 develop。该分支则是最新的开发进度

master分支仅在初始化代码仓库时使用，后续以release分支为准

**1.2**.辅助分支

1.2.1 feature分支 。

临时分支，该分支通常从develop分支派生。实现具体模块功能开发，模块完成之后，合并到 develop 分支，然后删除该分支。

1.2.2 hotfix分支。

临时分支，该分支通常从release分支或版本tag派生。该分支用来线上的紧急 bug 修复。当线上版本出现问题，将基于对应版本tag，创建 Hotfix 分支，问题修复后，合并回 release 和 develop ，同时打一个新的版本tag，标记为最新的线上发布版本。然后删除该分支。

注:开发者可自行命名分支名，要求分支名以feature-、release-、hotfix-开头。

#### **2.**jenkins操作流程和分支管理

jenkins主要分为3类操作，包括：

**2.1** develop分支构建管理

开发，测试，策划人员均可以执行develop分支的构建操作，包括策划表,代码等

**2.2** release分支构建管理

develop分支稳定后，测试人员可基于develop分支派生出新的release分支，并可执行相应的jenkins 任务进行release相关代码或策划表的构建，同时开发人员也在release修复测试bug，并在测试验证后合并到develop分支，最后测试通过后，测试人员执行jenkins 任务，将release代码合并到develop分支和master分支，并打出可发布版本tag。

**2.3** hotfix分支构建管理

开发人员基于线上版本tag或release分支派生出hotfix分支，代码修复后通知测试人员，测试人员执行相应的jenkins 任务进行代码构建，并在测试验证后合并到develop分支，最后测试通过后，测试人员执行jenkins 任务，将hofix分支代码合并到develop分支和release分支，并打出可发布版本tag。

**2.4** 打包发布正式版本

打线上正式包时，必须选择可发布版本tag，jenkins基于该tag打正式包。由于client通常是增量发布，client打包时还需要额外指定上个线上版本tag参数，不指定该参数则打全量包。

**2.5** 注意

1.修复bug时，必须先确认待修复的分支，release-分支提交的bug只能在release上分支上修复，修复后再合并回develop分支;develop分支上的bug只能在develop分支上修复; 



# 常用配置和地址

 

# elk使用

 

# 每日工作流程 

协议定义，生成和提交

 

# 业务实现规范

## 1.不相信客户端

## 2.批量操作mysql和redis

## 3.不能忽略error，必须返回和检验error

## 4.业务操作顺序，先判断是否已经领取过或已达成，并置为已领取标志后，才允许发放物品

## 5.浮点数和redis

## 6.战力值，伤害值存放类型必须为int，protobuf协议部分必须定义为int64或double

## 7.redis排行榜同分处理





# code review 常用检查清单

------

Structure

- Does the code completely and correctly implement the design?
- Does the code conform to any pertinent coding standards?
- Is the code well-structured, consistent in style, and consistently formatted?
- Are there any uncalled or unneeded procedures or any unreachable code?
- Are there any leftover stubs or test routines in the code?
- Can any code be replaced by calls to external reusable components or library functions?
- Are there any blocks of repeated code that could be condensed into a single procedure?
- Is storage use efficient?
- Are symbolics used rather than “magic number” constants or string constants?
- Are any modules excessively complex and should be restructured or split into multiple routines?

Documentation

- Is the code clearly and adequately documented with an easy-to-maintain commenting style?
- Are all comments consistent with the code?

Variables

- Are all variables properly defined with meaningful, consistent, and clear names?
- Do all assigned variables have proper type consistency or casting?
- Are there any redundant or unused variables?

Arithmetic Operations

- Does the code avoid comparing floating-point numbers for equality?
- Does the code systematically prevent rounding errors?
- Does the code avoid additions and subtractions on numbers with greatly different magnitudes?
- Are divisors tested for zero or noise?

Loops and Branches

- Are all loops, branches, and logic constructs complete, correct, and properly nested?
- Are the most common cases tested first in IF- -ELSEIF chains?
- Are all cases covered in an IF- -ELSEIF or CASE block, including ELSE or DEFAULT clauses?
- Does every case statement have a default?
- Are loop termination conditions obvious and invariably achievable?
- Are indexes or subscripts properly initialized, just prior to the loop?
- Can any statements that are enclosed within loops be placed outside the loops?
- Does the code in the loop avoid manipulating the index variable or using it upon exit from the loop?

Defensive Programming

- Are indexes, pointers, and subscripts tested against array, record, or file bounds?
- Are imported data and input arguments tested for validity and completeness?
- Are all output variables assigned?
- Are the correct data operated on in each statement?
- Is every memory allocation deallocated?
- Are timeouts or error traps used for external device accesses?
- Are files checked for existence before attempting to access them?
- Are all files and devices are left in the correct state upon program termination?

#  小游戏对接异常问题

百度小游戏：
文档地址：https://smartprogram.baidu.com/docs/develop/tutorial/codedir/

问题1：在安卓部分机型，游戏加载后闪退。
    原因：游戏加载的data.json过大，造成内存奔溃。

​    解决办法：第一种，拆分data.json,拆成多个文件。每个文件最好不大于5M。拆分后注意data.json文件中'_endTag'有一个下划线也会引起解析报错。生成data.json文件时，可改为使用'__endTag'，使用两个下划线。
​    解决方法：第二种，项目缩减data.json体积。将部分配置放到服务器端，有协议请求下发。
​    备注：可先使用方法1，对项目进行快速处理

问题2：IOS卡住不动。没有任何响应
    原因：百度APP问题，目前百度在修复中

问题3：游戏中，部分资源会提示加载失败，重试后还是不行。会导致游戏一些界面打开过程中一直转圈，卡在加载阶段。
    原因：百度APP问题，目前百度在修复中
    解决办法：将项目配置的加载超时时间设置长一些。比如设置为20000ms

vivo快游戏：
文档地址：http://minigame.vivo.com.cn/documents/lesson/start.html#%E5%88%9B%E5%BB%BA%E9%A1%B9%E7%9B%AE

问题1：加载出错，加载的URL是A的，返回的资源却是B资源的。
    原因：猜测Laya.load中，加载是复用http请求。vivo的运行环境可能对复用的http有一定的兼容性问题，导致加载出错
    解决办法：手动修改laya.load中，对发起的http处理。不复用http.每次发起load都创建新的http。将之前的http释放掉

问题2：屏幕适配显示不全。画面没有拓展到整个屏幕。(使用vivo提供的通用补丁后可解决,但引发了延伸问题1)
    延伸问题1：使用文档中的解决方案会，画面拉伸。(使用vivo提供的专属补丁后可解决，引发了延伸问题2)
    延伸问题2：游戏中部分列表内容显示不出来。（已按照vivo提供的代码进行修改）
    延伸问题3：列表上下拖动，会出现显示超出列表范围的情况(未解决)
    延伸问题4：界面遮盖有问题，比如主界面左下角的血量显示
    原因：vivo快游戏兼容性问题。没有laya的mini.js文件。一些适配需要项目组配合vivo来具体执行。
    解决方法：按照vivo提供的代码，对laya引擎进行code修改
    

问题3：文字显示不清晰。（文字带有描边的话，就能够显示清晰）
    原因：vivo兼容性问题。
    解决办法：测试后发现带有描边的文字可显示清晰。就对文本设置了默认描边。这样显示就清晰了

问题4：组件input.blur报错。导致游戏奔溃
    原件:vivo兼容性问题
    解决办法：按照vivo提供的代码，对laya引擎进行code修改

问题5：vivo文字位置错位
  原因：vivo兼容性问题
  解决办法: 按照vivo提供的代码，对laya引擎进行code修改。修改WebGLCharImage中 CborderSize的值，改为0.


问题6：遮盖有问题，比如主界面左下角的血量显示（问题2的延伸问题）
问题7：列表上下拖动，会出现显示超出列表范围的情况（问题2的延伸问题）
    原因：vivo兼容性问题。需要vivo进行解决
    解决办法：暂无。
问题8：界面打开前转圈，疑似加载慢
    原因：猜测1：未启用worker  猜测2：load加载都是发起的新创建的http  猜测3：游戏项目并发加载量太多，大于加载最大数造成队列堆积（可对游戏资源加载设定优先级，比如界面资源优先级为0）
    

头条小游戏：
文档：https://developer.toutiao.com/docs/

问题1：充值档位和游戏内不匹配（属于游戏功能问题，非兼容性问题）
    原因：头条提供的充值档位和游戏原有设计的不匹配
    解决办法：项目组考虑兼容充值档位。由项目组进行修改

oppo小游戏：
文档：https://cdofs.oppomobile.com/cdo-activity/static/201810/26/quickgame/documentation/

问题1：打包rpk后进行测试出现黑屏
  原因：mini没有初始化
  解决办法：正确的初始化mini文件

问题2：引用的lodash、promise库在oppo中报错
    原因：引用库中使用了Function, oppo不支持使用Function
    解决办法：手动修改lodash库，将Function形式的函数改为function形式的匿名函数

问题3：patch.ts中 对iphoneX的判定报错
    原因： patch.ts中队inIphoneX的判定调用了wx的方法。在oppo中不存在
      oppo的window.navigator.userAgent中并没有可标示出是小游戏的字段。
    解决办法：去掉了patch中iphoneX的判定
问题4：websocket连接失败
    原因：oppo暂时不支持wss方式的websocket链接。
    解决办法：改为使用ws的方式进行连接

问题5：输出框无法唤起键盘
    原因：oppo项目下main.js中有串修改window.navigator.userAgent的值。这个代码段不能缺少。此段代码中包含了minigame的判定。是用来初始化laya引擎的onMiniGame变量的
    解决办法：按照示例的main.js，将顶部的代码段包含进来

​    


# 线上Bug list

