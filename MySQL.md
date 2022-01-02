# Ubuntu安装MySQL

## 1.更新源

`sudo apt update`

## 2.安装mysql

`sudo apt install mysql-server mysql-client`

## 3.设置密码

#### (1). 查看默认值
`sudo cat /etc/mysql/debian.cnf`

#### (2). 使用默认值登录
`mysql -udebian-sys-maint -pDp84LaxPU0cY2P7p`

#### (3). 添加用户
* `use mysql;`
* `update mysql.user set authentication_string=password('123456') where user='root' and Host ='localhost';`
* `update user set  plugin="mysql_native_password";`
* `flush privileges;`
* `quit`

#### (4). 使用新用户登录
`mysql -uroot -p123456`

# 启动和关闭mysql

## 1.启动mysql

1. `sudo /etc/init.d/mysql start`
2. `sudo service mysql start`

## 2.关闭mysql

1. `sudo /etc/init.d/mysql stop`
2. `sudo service mysql stop`

## 3.重启mysql

1. `sudo/etc/init.d/mysql restart`
2. `sudo service mysql restart`

# 连接windows子系统的mysql

## 1. 查看mysql端口

1. `mysql -uroot -p123456`
2. `show global variables like 'port';`

## 2. 退出并关闭mysql

## 3. 更改端口号（windows已安装过mysql）

1. `vim /etc/mysql/mysql.conf.d/mysqld.cnf`
2. `sudo /etc/init.d/mysql start`
3. `mysql -uroot -p123456`
4. `show global variables like 'port';`

## 4. windows cmd/DBeaver连接mysql

1. `mysql -h localhost -P 3333 -u root -p`


