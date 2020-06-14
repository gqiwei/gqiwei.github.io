---
title: liunx配置jdk，mysql傻瓜式流程
categories:
  - 笔记
tags:
  - linux
date: 2020-01-03 14:17:27
---

两年前的阿里云，里面环境太老了比如python，然后因为不是专门的运维，不会升级，所以把服务器重置，系统选了新的centos，重新配置环境，并记录下。  
阿里云CentOS 7.7 64位。
连接工具xshell，xftp。<!--more -->
# 配置jdk
压缩包是自己多年保存下来的，jdk1.8。可以去官网下载。
``` shell
cd /usr
mkdir java
cd java
```
然后将jdk压缩包用xftp拖上去。  
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/liunx/1.png)
```
tar -zxvf jdk-8u111-linux-x64.tar.gz
```
配置环境变量
```
vim /etc/profile
```
把下面配置添加到文件中
```
export JAVA_HOME=/usr/java/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib
```
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/liunx/2.png)  
如果忘记vim如何使用，进入[菜鸟教程](https://www.runoob.com/linux/linux-vim.html)重新学习。
保存好文件后
```
source /etc/profile
```
查看java版本  
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/liunx/3.png)


# 安装mysql
安装
```
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server
```
启动
```
systemctl start  mysqld.service
```
查看运行状态
```
systemctl status mysqld.service
```
寻找root用户密码
```
grep "password" /var/log/mysqld.log
```
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/liunx/4.png)
登录
```
mysql -uroot -p
```
![](https://xiaoguaiblog.oss-cn-shanghai.aliyuncs.com/%E5%B0%8F%E6%80%AA%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87/liunx/5.png)
修改密码
```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```
修改密码会提示`ERROR 1819 (HY000): Your password does not satisfy the current policy requirements`，这是密码设置规范的原因,设置成字母大小写数字加特殊符号。  
查看密码设置规则
```
SHOW VARIABLES LIKE 'validate_password%';
```
创建授权
```
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
flush privileges;
```
在阿里云设置了安全组，mysql账号权限也放开后，外部连不上mysql，我这里是直接关闭了防火墙。
```
systemctl stop firewalld
```