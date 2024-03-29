# aT0ngMu的博客

![travis-ci-status](https://travis-ci.com/Vancir/blog.svg?branch=master)

建立博客的目的在于记录,分享和传播有趣的内容, 在不断地文字编写过程中, 凝练自己的想法并倾注于笔下. 

* 出于对Markdown的喜爱, 我选择使用静态博客. 
* 静态博客中, 我选择hexo是出于其丰富的生态以及灵活简单的环境配置.(以前也有用Jekyll, 但是我觉得太过死板, 感觉还是不太好) 
* 主题我采用了简洁的[cactus主题](https://github.com/probberechts/hexo-theme-cactus)并稍作了些修改, 以便更符合我的习惯.  
* 博客部署使用Travis来进行持续集成. 这在[Hexo官方文档](https://hexo.io/docs/github-pages.html)中有对应说明.

## 快速使用

安装前你需要安装好`node.js/npm/cnpm`
    
``` bash
# 安装hexo工具
npm install -g hexo-cli --registry=https://registry.npm.taobao.org

# 克隆代码并安装npm包
git clone https://github.com/aT0ngMu/aT0ngMu.github.io.git
cd blog
npm install --registry=https://registry.npm.taobao.org
```

安装好后使用以下命令:

``` bash
# 生成静态博客页面
hexo generate

# 启动服务访问博客
hexo server
```

然后访问`http://localhost:4000`就是目前的静态博客页面了.

如果想清除生成的静态页面. 可以运行`hexo clean`.

## 功能改进

* 增加了插件用于生成RSS
* 增加了插件用于统计文章字数阅读时间
* 修改了代码块配色, 不会像原版一样是突兀的方框