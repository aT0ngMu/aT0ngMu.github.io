---
title: CTF中上传绕过各种姿势（未完待续……）
date: 2018-11-20 04:00:00
tags:
- 大小写绕过
---

之前做过的一个比较简单的文件上传，上传图片木马后，直接bp改包，利用%00截断改后缀就可以得到flag。

[00截断题目](http://ctf5.shiyanbar.com/web/upload/)

[00截断wp](https://blog.csdn.net/wy_97/article/details/76549405)

今天遇到了一个需要绕过技巧的文件上传，所以，简单学习记录一下。

[题目来自于bugku](http://120.24.86.145:8002/web9/)

解决思路：首先上传一个图片木马，bp抓包后，需要修改两处：

![1.png](https://i.loli.net/2021/08/11/wmRnGVW6OId7uki.png)

分别对上述两处更改做一下解释：

小写改为大写：类型检测大小写绕过

当时做题的时候有两个地方不是很清楚，1、为什么要把小写改成大写。2、为什么会出现两个content-type，这两个又有什么区别

1. 后来经大佬提点，可能是服务器端验证，也就是代码校验，很多带waf的网站，在进行文件上传的时候。改了大小写以后就可以进去，有的时候会是content-type的multipart改为Multipart，有的时候是file改为File，还有把-改为+。这些都是与后台的代码有关。
2. 第二个问题解决的时候，查了很多的资料，但是最终都搞清楚了。

简单来说，上面的就是在表单上传文件时的类型，下面的就是上传文件时的文件类型。

具体的请参考：https://www.jianshu.com/p/29e38bcc8a1d

后缀名改为.php5

利用过滤黑名单的方式防止上传。如果直接上传phtml，绕过黑名单，phtml还被能解析成php。符合的后缀包括 php、php3、php4、php5、phtml、pht等

原理：在php5.conf文件中有相关php后缀的正则，只要符合该正则的php后缀，都可以被解析。

另外附上PHP文件上传过程中基于白名单、黑名单、基于MIME的认证以及目录认证的代码样例：https://blog.csdn.net/hitwangpeng/article/details/47060671

另，个人认为这是比较全的上传绕过的姿势了

[https://thief.one/2016/09/22/%E4%B8%8A%E4%BC%A0%E6%9C%A8%E9%A9%AC%E5%A7%BF%E5%8A%BF%E6%B1%87%E6%80%BB-%E6%AC%A2%E8%BF%8E%E8%A1%A5%E5%85%85/](https://thief.one/2016/09/22/上传木马姿势汇总-欢迎补充/)
