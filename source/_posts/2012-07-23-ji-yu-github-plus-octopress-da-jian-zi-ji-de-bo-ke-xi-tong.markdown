---
layout: post
title: "基于 GitHub + Octopress 搭建博客系统"
date: 2012-07-23 03:19
meta: true
comments: true
categories: Summary
tags: github octopress blog
---

## 缘起

据说每一个使用[Octopress][]写博客的码农，第一篇博客必然是讨论Octopress本身，既在江湖混，咱也遵循江湖规矩。

使用[GitHub Pages][]和Octopress搭建自己的博客系统在近期技术圈非常的流行。用Git写Blog~~~一听就酷得没边了，再加上[GitHub][]和[MarkDown][]，绝对的码农神器。

加上自己之前在乱七八糟的地方都是写几篇就不玩了，最后甚至连博客帐号都忘了。。。这次痛下决心，自己老老实实整一个，即使不写了，也还在GitHub里。

<!-- more -->

## 简介 

整个系统基于官方的分支，主要针对自己的习惯做了以下定制：

* 修改Octopress使Category能完美的支持中文
* 根据Octopress的Category，加了一个类似的Tag机制
* 在sidebar加入Category列表和Tag Cloud机制
* 加入对[JiaThis][]和[多说][]的支持


#### 说明 

其实Category在Octopress里面本身就是传统意义上的tag机制，不过个人依然比较喜欢传统的目录机制，所以分成两个，一个用来表示目录，一个用来表示Tag。然后我写的时候每篇文章只属于一个Category，但是会有多个Tag。

分享和评论部分也可以使用[Disqus][]、[友言][]， 友言的好处是支持缩进式评论，缺点是在我这儿加载比较慢，ui也不太符合我的口味。另外JiaThis和Octopress的Video模块有冲突，会造成分享那儿有个小小的flash框，视觉上超级不爽。最后我直接修改js跳过了对JiaThis Object的处理

整个过程中参考了<http://mrzhang.me>和<https://github.com/tokkonopapa/octopress-tagcloud>的定制。


## 安装

### 1. 准备

#### 必须
* 一个方便使用Git, ruby, vim的系统，Linux/Mac OS X都不错，用windows的就自觉点安个虚拟机
* 注册一个[GitHub](https://github.com/signup/free)帐号，新建一个空的Repository。如果你准备用自己的域名的话，Repository的名称可以随便取。另外，GitHub提供二级域名，如果要使用二级域名，必须把Repository取名为 你的GitHub帐号名.github.com，而且这样同时也能支持你的个人域名

#### 可选
* 一个多说或者Disqus、友言的帐号，用于管理你的评论系统
* 如果想用自己的域名，把source/CNAME文件内的域名改成你的域名，并且把该域名的A Record指向IP：207.97.227.245，域名和CNAME文件的内容必须完全一致。比如我把我的域名 konglh.com指过来了，那么CNAME里面必须写konglh.com，不能写成www.konglh.com之类

### 2. 安装ruby
参考文档：<http://octopress.org/docs/setup/rvm/>

#### 为当前用户安装rvm
{% codeblock lang:bash %}
bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
{% endcodeblock %}

#### 将rvm加入你的shell环境
{% codeblock lang:bash %}
echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm" # Load RVM function' >> ~/.bash_profile
source ~/.bash_profile
{% endcodeblock %}

#### 安装RVM
{% codeblock lang:bash %}
rvm install 1.9.3 && rvm use 1.9.3  # Octopress required that rvm version >= 1.9.2
rvm rubygems latest
{% endcodeblock %}


### 3. 安装Octopress
以下的安装均基于本人定制过的分支

#### 下载Octopress
{% codeblock lang:bash %}
git clone git://github.com/psaux/octopress.git -b konglh
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
ruby --version  # Should report Ruby 1.9.3 or 1.9.2 if you install 1.9.2
{% endcodeblock %}

#### 安装依赖
{% codeblock lang:bash %}
gem install bundler
bundle install
{% endcodeblock %}

#### 安装默认主题
目前我是直接修改的默认主题，所以安装方法一致。其实这步就是把.themes/classic/内的两个目录复制到项目根目录
{% codeblock lang:bash %}
rake install
{% endcodeblock %}

它其实类似于下面两条命令的合体，如果你修改主题，需要了解这两个命令，如果不想改，无视这儿就好:
{% codeblock lang:bash %}
rake update_source['classic']
rake update_style['classic']
{% endcodeblock %}


### 4. 修改个人配置
所有配置项都在\_config.yml文件，改成你的数据即可，所有的配置都有注释，一看就懂。

到现在，你已经可以在本地测试了，


### 5. 创建文章并本地测试
现在我们可以验证我们的本地系统已经能正常工作。
另外，以后我们码字时，可以先在本地起一个服务，预览无误后再部署

{% codeblock lang:bash %}
rake new_post[‘article name’] # 按照[]中的名字生成一篇新的文章，然后修改生成的文件即可
rake generate   # Generates posts and pages into the public directory
rake watch      # Watches source/ and sass/ for changes and regenerates
rake preview    # Watches, and mounts a webserver at http://localhost:4000
{% endcodeblock %}

其中`rake new_post`生成的文章在 soure/_posts目录下，Octopress会根据当前日期和输入的文章标题自动命名，s生成文件的路径名会显示终运行结果中。形如：

{% codeblock %}
rake new_post['test']
mkdir -p source/_posts
Creating new post: source/_posts/2012-07-23-test.markdown
{% endcodeblock %}

运行 `rake preview` 后，你就可以使用<http://localhost:4000>访问你的本地博客了。


### 6. 连接GitHub 

**注意**：如果用自己的域名，记得把source/CNAME文件内的域名改成你的域名

{% codeblock lang:bash %}
rake setup_github_pages
{% endcodeblock %}

它会提示你输入有读写权限的Repository Url，这个在你的GitHub为Blog建立的这个Repository上可以找到。Url形如：git@github.com:[Username]/[Repository].git

### 7. 部署

{% codeblock lang:bash %}
rake generate
rake deploy
{% endcodeblock %}

当看到“Github Pages deploy complete”后，就表示部署大功告成。注意GitHub会过一段时间才能给你生成，所以不要急，等个几分钟再看。如果是用的自定义域名的话，第一次的时间会更久，可能会到小时级。

**Tips**：Octopress提供的所有rake方法，可以运行`rake -T`查看。


### 8. 备份

既然使用了GitHub，我们的文章自然要用它管理起来，不然太浪费了。

{% codeblock %}
cd source/_posts/
git init
touch README.md
git add *
git commit -m 'first commit'
git remote add origin git@github.com:[Username]/[Repository].git
{% endcodeblock %}

_posts目录是Octopress里文章的默认保存目录。commit即可，无需push。接下来我们创建并切换到备份分支store，名字根据你的兴趣随便取，以后我们就使用这个分支管理我们的文章，整个流程如下：

{% codeblock lang:bash %}
git checkout -b store
git add *.markdown
git commit -m 'backup'
git push origin store
{% endcodeblock %}


### 9. 补充

[zespia][]同学的Blog的主题设计清爽淡雅，非常符合我的审美，现在把它移植到了Octopress中，主题名hexo。BTW，感谢psefxx@github的协助，前端我还是很不懂的，→_→。

使用方法如下： 

{% codeblock lang:bash %}
rake update_source['hexo']
rake update_style['hexo']
{% endcodeblock %}

## Over


[GitHub]:       https://github.com/         "GitHub"
[GitHub Pages]: https://pages.github.com/   "GitHub Pages"
[Octopress]:    http://octopress.com/       "Octopress"
[MarkDown]:     http://daringfireball.net/projects/markdown/       "MarkDown"
[Disqus]:       http://www.disqus.com/     "Disqus"
[JiaThis]:      http://www.jiathis.com/     "加网"
[zespia]:       http://zespia.tw/           "zespia"
[多说]:         http://www.duoshuo.com/     "多说"
[友言]:         http://www.duoshuo.com/     "友言"
