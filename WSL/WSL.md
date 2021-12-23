# WSL（windows下安装ubuntu子系统）

## 打开并下载子系统

![image_text](https://github.com/zhang0xf/md/blob/main/WSL/image/image1.png)

![image_text](https://github.com/zhang0xf/md/blob/main/WSL/image/image2.png)

![image_text](https://github.com/zhang0xf/md/blob/main/WSL/image/image3.png)

![image_text](https://github.com/zhang0xf/md/blob/main/WSL/image/image4.png)

## 换源

#### 1.备份
`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

#### 2.打开文件
`sudo vim /etc/apt/sources.list`

#### 3.替换内容（即换成阿里源）
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

#### 4. 更新
`sudo apt-get update`

## 基础开发环境

#### 1.安装gcc
`sudo apt install gcc`

`gcc -v`

#### 2. 安装vscode
略

#### 3. vscode安装插件remote -wsl
略

#### 4. install vscode server

`cd "your project"`

`code .`

#### 5. 【推荐】更新wsl2

1. 查看版本信息：`wsl -l -v`

2. 启用虚拟机功能, 使用 PowerShell 以管理员身份运行：`dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart`

3. 重启电脑

4. 下载linux内核更新包：[msi文件](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

5. 运行安装包

6. 将 Ubuntu18.04 设置为WSL2：`wsl --set-version Ubuntu-18.04 2`

7. 查看版本信息：`wsl -l -v`

## 参考

[https://www.cnblogs.com/stulzq/p/13926936.html](https://www.cnblogs.com/stulzq/p/13926936.html)

