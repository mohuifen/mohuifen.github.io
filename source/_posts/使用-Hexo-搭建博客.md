title: 使用 hexo 搭建博客
date: 2015-12-23 11:39:45
categories: 教程
tags: hexo
---
<br>
**时间：2015年12月23日**

**作者：秋儿（lvruifei@foxmail.com）**

<br>

####	配置环境

 安装 homebrew：

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	
安装node.js：

	brew install node

安装 hexo:

	npm install hexo -g

<!-- more -->

执行init命令初始化hexo到你指定的目录

	hexo init <folder>
	
进入文件夹

	cd <folder>
然后运行

	npm install //这句应该用不到
	
自动根据当前目录下文件,生成静态网页

	hexo generate
	
运行本地服务

	 hexo server 
	 
Done！
<http://localhost:4000>


//停止本地服务：Press Ctrl+C to stop


<br>
####	添加文章

创建文章文件名

	hexo new ""
	
生成静态网页

	hexo generate
	
运行本地服务
	
	hexo server


###	部署到 Github

这篇文章很详细
<http://jingyan.baidu.com/article/d8072ac47aca0fec95cefd2d.html>

######	关键工作
配置 _config.yml 文件

	deploy:
 	type: git
 	repository: https://github.com/XXXXXX/XXXXXX.github.io.git
 	branch: master


生成网页

	hexo generate
	
部署
	
	hexo deploy

