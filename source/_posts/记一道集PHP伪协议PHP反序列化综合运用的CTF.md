---
title: "记一道集PHP伪协议及PHP反序列化综合运用的CTF"
date: 2018-11-19 02:00:00
tags:
- PHP伪协议
- 反序列化
---

首先拿到题目后，毫无疑问，查看一下源码

``` php
<!--  
$user = $_GET["txt"];  
$file = $_GET["file"];  
$pass = $_GET["password"];    
if(isset($user)&&(file_get_contents($user,'r')==="welcome to the bugkuctf")){  
echo "hello admin!<br>";  
include($file); //hint.php  
}else{  
echo "you are not admin ! ";  
}  
-->
```

解读一下源码的意思：

1. get方式传入三个参数：txt，file，password
2. file_get_contents()函数的作用是将$user的内容读取出来，在此即为将user读取出来的内容数值与类型完全等于 `welcome to the bugkuctf`（三个等于号）
3. 如果满足if条件，则打印输出”hello admin!”。并且将包含的hint.php内容显示出来

------

在此，可以运用两个PHP的伪协议

* php://filter 可以进行任意文件的读取。

[有关php://filter的详细了解](https://www.leavesongs.com/PENETRATION/php-filter-magic.html)

* php://input 可以读取没有处理过的post数据

通过对以上代码的分析，我们有了解决思路：

* 对于file_get_contents($user,’r’)===”welcome to the bugkuctf”，我们可以借用php伪协议php://input，要想使user中的字符串读取出来与三等号后的内容一致，就必须使他的传入参数txt，来post welcome to the bugkuctf

* 再来看include($file); //hint.php 我们可以用php://filter来进行hint.php的base编码，从而可以读取出hint.php的base编码，从而可以读取出hint.php的内容。**

构造payload:

```
index.php?txt=php://input&file=php://filter/read=convert.base64-encode/resource=hint.php
```

利用bp改包

![1.png](https://i.loli.net/2021/08/11/8Fpr2NfGheadXyj.png)

得到回显

![2.png](https://i.loli.net/2021/08/11/oVi7pbluDk8LMqR.png)

发现一组base64,再进行base64解码，发现又有一组代码

```php
#hint.php

<?php  

class Flag{//flag.php  
public $file;  
public function __tostring(){  
    if(isset($this->file)){  
        echo file_get_contents($this->file); 
        echo "<br>";
    return ("good");
    }  
}  
}  
?>
```

再来分析这段代码

看到flag.php，猜测flag或许就从这段代码中找出。发现了比较关键的一个函数**tostring(),此函数的作用是将Flag类作为字符串执行时会自动执行此函数，并且将变量$file作为文件名输出文件内容。但是，这个时候发现并没有代码显示可以调用这个类，如果没有调用这个类，也就无法执行**tostring()函数。而且，还有一个password参数没有用上，所以考虑，或许还会存在另一段代码。那就接着上一步，把hint.php换成index.php会有什么发现呢？

果不其然，确实又有了重大发现：

![3.png](https://i.loli.net/2021/08/11/k41uCXsiTl3ja2o.png)

再次得到回显

![4.png](https://i.loli.net/2021/08/11/9fxMITFDy8WkvRl.png)

解码后

```php
index.php

<?php  
$txt = $_GET["txt"];  
$file = $_GET["file"];  
$password = $_GET["password"];  

if(isset($txt)&&(file_get_contents($txt,'r')==="welcome to the bugkuctf")){  
echo "hello friend!<br>";  
if(preg_match("/flag/",$file)){ 
    echo "ä¸è½ç°å¨å°±ç»ä½ flagå¦";
    exit();  
}else{  
    include($file);   
    $password = unserialize($password);  
    echo $password;  
}  
}else{  
echo "you are not the number of bugku ! ";  
}  

?>  

<!--  
$user = $_GET["txt"];  
$file = $_GET["file"];  
$pass = $_GET["password"];  

if(isset($user)&&(file_get_contents($user,'r')==="welcome to the bugkuctf")){  
echo "hello admin!<br>";  
include($file); //hint.php  
}else{  
echo "you are not admin ! ";  
}  
 -->
```

结果发现了正则匹配函数preg_match(“/flag/“,$file)，对flag进行了正则匹配。

再来接着看，else代码块中又一次包含了$file,并且对$password进行反序列化。通过对上述hint.php的解读，我们就可以构造password，password为Flag类型，字符串变量file=flag.php。构造的序列化对象payload，password=O:4:”Flag”:1:{s:4:”file”;s:8:”flag.php”;}

![5.png](https://i.loli.net/2021/08/11/HCewWuc58MpJohf.png)

在这里简单说一下php的反序列化

将原来的某个对象进行序列化之后，从序列化后的结果中就可以知道这个对象的具体类型和值

在这里简单说一下序列化后的几个字母的意思，以password=O:4:”Flag”:1:{s:4:”file”;s:8:”flag.php”;}为例

* O（大写）：对象class
* 4：4个字符
* “Flag”：对象名
* 1：数量，一个
* s：string
* {}里的为参数

反序列化可以理解为序列化的的逆运算
