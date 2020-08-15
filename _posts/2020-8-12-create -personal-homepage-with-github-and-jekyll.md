---
layout: post
title: "Github + Jekyll 搭建个人主页"
date: 2020-8-11
author: Tianlh
header-img: "img/head-bg1.jpg"
tags:
  - Github
  - Jekyll
  - 个人主页
---

### **Tianlh的个人主页**
&ensp; [**我的个人主页**](https://tianlh.github.io/)
![image]({{ "/img/post/43857a8d5c27670cbb1130e070a0bbe8.png" }})

### **使用Github Pages创建个人主页**
在Github上选择”Start a project“新建一个仓，输入”Repository name“，仓名的格式为“Gihub用户名.github.io”，点击“Create repository”创建完毕。
![image]({{ "/img/post/e21190da40c7e8d40180322b1b637e45.png" }})
点击“Settings”，找到GitHub Pages的相关设置：其中可以看到关键词“Your site isp published at XXXXX”，这是你的个人主页地址；“Source“栏的”Branch“项可以选择默认的构建分支；”Theme Chooser“可以选择使用一些已有的主题来创建个人主页。这里，我没有使用默认的主题，而是使用Jekyll，改用了其他模版来创建个人主页。
![image]({{ "/img/post/4d6b3932e78fbfd72507369afc423fdc.png" }})

### **Jekyll创建静态网页**

#### **Mac OS下安装Jekyll**

##### 安装Ruby和RubyGems

###### - 可以通过RVM（多版本Ruby环境管理和切换工具）安装
- 安装RVM，在控制台终端执行命令：\curl -sSL https://get.rvm.io&#124;bash -s stable
- 执行名令查看支持的Ruby版本列表：rvm list known
- 选择一个版本安装：rvm install 2.6
- 安装完执行命令查看已安装的Ruby版本：rvm list

###### - 也可以万能的Homebrew直接安装：
- brew install ruby
- 如果Homebrew更新太慢的话建议换个国内的更新源：
<br/># 替换brew.git
<br/>cd "$(brew --repo)"
<br># 替换中国科大更新源
<br/>git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
<br/># 替换清华大学更新源
<br/>git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
<br/>替换homebrew-core.git:
<br/>cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
<br/># 替换中国科大更新源
<br/>git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
<br/># 替换清华大学更新源
<br/>git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

###### - 安装完Ruby建议替换成国内源
- 查看当前源
<br/> gem sources -l
- 删除当前源
<br/> gem sources --remove http://rubygems.org/
- 添加国内源
<br/>gem sources -a http://gems.ruby-china.com/
- 确认替换成功
<br/> gem sources -l
- 几个国内源都可以试试，看哪个能用用哪个：
<br/>http://mirrors.aliyun.com/rubygems/
<br/>http://gems.ruby-china.com/
<br/>http://ruby.taobao.org/

##### 安装NodeJS
- 去官网https://nodejs.org/en/下载最新稳定版本
![image]({{ "/img/post/1ea83cfe023f7ba8c5bccb0e938a5c05.png" }})
- 双击下载的安装包，按提示安装就行
- 安装完毕，在终端执行命令查看是否出现正确版本号：
<br/>npm -v
<br/>node -v

##### 安装Python 2.7
- Mac OS应该是自带Python 2.7，终端执行Python检查下版本号对不对，没有的话执行brew install python安装

##### 用RubyGems安装Jekyll
- 在终端下执行：gem install jekyll，如果前几步改了国内源的话应该很快就能安装完了
- 执行命令时

##### 安装 Xcode Command-Line Tools
- 直接App Store安装Xcode即可

至此终于把环境配置完毕。

#### **创建个人主页**
安装完Jekyll就可以自己写个人主页了，可以参考官方教程https://jekyllrb.com/(英文) 或者http://jekyllcn.com/(中文)，也可以参考已有的模版修改。我是在https://github.com/caojiele/Jlcaoblog-boilerplate  的基础上修改的，慢慢摸索增加了个静态相册j页面。
<br/><br/>在本地clone最开始在Github上创建的个人主页仓。
<br/><br/>在本地仓目录下运行终端，执行命令 jekyll build --watch，当前文件夹中的内容就会生成到./_site 文件中，并且会对目录进行监控，检测到修改，自动再生成。
<br/><br/>执行命令jekyll serve，就会生成一个开发服务器，运行在 http://localhost:4000/，浏览器打开就可以浏览最终效果。
<br/><br/>当前仓目录下会生成./_site和./.jekyll-cache等目录，这些文件只是本地缓存文件，不需要提交，记得加到gitignore里。全部修改完毕提交到Github远程仓。重新打开个人主页就可以看到最新的修改效果啦。
