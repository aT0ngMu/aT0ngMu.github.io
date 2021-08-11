---
title: 集lamp搭建、DVWA搭建、sqli-labs搭建配置教程及常见问题解决之二
date: 2018-11-20 01:00:00
tags:
- DVWA搭建
- lamp搭建
- sqli-labs
---

在上一篇搭建好web环境后，再进行Ubuntu下DVWA的搭建，在搭建过程中会出现各种各样的问题。以下是本菜鸡在搭建过程中出现的问题以及解决办法~

首先在GitHub下载安装DVWA，压缩包放到 /var/www/html之后解压缩

``` shell
atongmu@atongmu-virtual-machine:~/Downloads$ sudo cp -i DVWA-master.zip /var/www/html
atongmu@atongmu-virtual-machine:/var/www/html$sudo unzip DVWA-master.zip
```

在浏览器输入127.0.0.1/DVWA-master/setup.php后页面显示：需要我们修改配置文件后缀名为.php

``` shell
atongmu@atongmu-virtual-machine:/var/www/html/DVWA-master/config$ sudo mv config.inc.php.dist config.inc.php
```

菜鸡装到这里，再重启apache2以后发现，在DVWA界面出现了很多PHP不完整代码，如同一个正常人长了天花一般触目惊心……后经分析原因是apache2无法解析PHP代码。到这里，关键一步就是：

``` shell
sudo apt-get install apache2
sudo apt-get install php7.2
sudo apt-get install libapache2-mod-php
```

第三条命令的目的就是：apache缺少PHP的延展模块，所以无法解析PHP~

之后，页面显示正常……

下一步开始修改下面的disable and no

![1.png](https://i.loli.net/2021/08/11/EXL3qH9g84kfPry.png)

第一处：

``` shell
sudo vi php.ini
把allow_url_include = off改为on
把allow_url_fopen = off改为on
```

第二处：

``` shell
sudo apt-get install php7.2-gd
sudo /etc/init.d/apache2 restart
```

第三处

``` shell
sudo apt-get install php7.2-mysql
sudo /etc/init.d/apache2 restart
```

在进行第二三处的时候请注意：搭建的dvwa的PHP版本是否是7.2，因为在之前添加apache的PHP扩展模块时，相当于安装了从5.6到7.2的所有模块，dvwa会自动匹配最高级版本，现在的PHP最高版本为7.2.

第四处：

``` shell
ls -al /var/www/html/DVWA-master/external/phpids/0.6/lib/IDS/tmp
sudo /etc/init.d/apache2 restart
sudo chmod 666 /var/www/html/DVWA-master/hackable/uploads
sudo chmod 666 /var/www/html/DVWA-master/external/phpids/0.6/lib/IDS/tmp/phpids_log.txt
sudo /etc/init.d/apache2 restart
sudo chmod 666 /var/www/html/DVWA-master/config
sudo /etc/init.d/apache2 restart
```

之后本以为会顺利搭完……菜鸡又出现了状况……

在点击下面的creat databases时，页面返回是……

![2.png](https://i.loli.net/2021/08/11/R3L7ehZ5PXty6DV.png)

之后才发现

![3.png](https://i.loli.net/2021/08/11/aFzs6Eiq2HMbJjl.png)

这里的password要与MySQL一致才可以……

重启apache，重新加载url，顺利创建……

以下列出dvwa的默认登陆账号与密码：

*admin/password* gordonb/abc123
*1337/charley* pablo/letmein
*smithy/password
