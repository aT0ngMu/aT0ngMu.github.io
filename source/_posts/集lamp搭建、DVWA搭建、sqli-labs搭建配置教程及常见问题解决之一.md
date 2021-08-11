---
title: 集lamp搭建、DVWA搭建、sqli-labs搭建配置教程及常见问题解决之一
date: 2018-11-20 00:00:00
tags:
- DVWA搭建
- lamp搭建
- sqli-labs
---

众所周知，我们在搭建DVWA时，要先搭建好web环境—LAMP（Linux+Apache2+Mysql+PHP5.6）。那么以下就是LAMP具体搭建步骤及问题解决方式。

第一步，安装并配置Mysql。安装顺利的话，直接输入以下三条命令：

``` shell
sudo apt-get install mysql-server
sudo apt isntall mysql-client
sudo apt install libmysqlclient-dev
```

但总会有不顺利的情况。。。

``` shell
eading package lists... Done
Building dependency tree       
   Reading state information... Done
   Some packages could not be installed. This may mean that you have
   requested an impossible situation or if you are using the unstable
   distribution that some required packages have not yet been created
   or been moved out of Incoming.
   The following information may help to resolve the situation:
   The following packages have unmet dependencies:
   mysql-server : Depends: mysql-server-5.1 but it is not going to be instaed

   E: Unable to correct problems, you have held broken packages.
```

解决办法：

``` shell
sudo vi /etc/apt/source.list
```

在此配置文件中上方添加以下内容：

``` shell
deb http://mirrors.aliyun.com/ubuntu/ precise main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ precise-security main restricted universe multiver 
deb http://mirrors.aliyun.com/ubuntu/ precise-updates main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ precise-proposed main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ precise-backports main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ precise main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ precise-security main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ precise-updates main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ precise-proposed main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ precise-backports main restricted universe multiverse
```

更新一下列表（这个过程比较漫长）

``` shell
sudo apt-get update  && sudo apt-get -y upgrade
```

再重新执行上述三条命令

------

下面测试一下mysql是否安装成功

``` shell
sudo netstat -tap | grep mysql
```

![1.png](https://i.loli.net/2021/08/11/t4zYgRTAmQuf2La.png)


进入MySQL服务

``` shell
mysql -uroot -p你的密码
```

第二步，安装PHP5.6。

执行以下命令

``` shell
sudo apt-get install python-software-properties 
sudo add-apt-repository ppa:ondrej/php 
sudo apt-get update 
sudo apt-get -y install php5.6 php5.6-mcrypt php5.6-mbstring php5.6-curl php5.6-cli php5.6-mysql php5.6-gd php5.6-intl php5.6-xsl php5.6-zip
```

最后查看一下php版本

``` shell
php -v
```

第三步，安装apache2

``` shell
sudo apt-get install apache2
```

等待安装完成……
