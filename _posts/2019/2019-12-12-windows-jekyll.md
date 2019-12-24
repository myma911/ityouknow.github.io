---
layout: post
title:  Windows下jekyll的安装和使用
no-post-nav: false
category: it
tags: [it]
copyright: it
excerpt: Jekyll 是一个简单的博客形态的静态站点生产机器
---



# Jekyll 究竟是什么

Jekyll 是一个简单的博客形态的静态站点生产机器。它有一个模版目录，其中包含原始文本格式的文档，通过一个转换器（如 [Markdown](http://daringfireball.net/projects/markdown/)）和我们的 [Liquid](https://github.com/Shopify/liquid/wiki) 渲染器转化成一个完整的可发布的静态网站，你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 [GitHub Page](http://pages.github.com/) 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是**完全免费**的。

# 在windows下安装jekyll

## 1.  安装 Ruby+devkit

下载地址：[https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)

下载安装包：rubyinstaller-devkit-2.5.5-1-x64.exe

版本太高会出现很多意想不到的问题

点击安装即可，在安装结束时，不要勾选ridk install的选项，后面再手动安装

检查ruby是否正常安装，打开cmd控制台，输入如下命令会出现版本号

``` ruby
ruby -v
```

检查gem是否安装完毕：
```
gem -v
```

## 2.  安装MSYS2
输入命令：

```
ridk install
```
输入“ridk install”进行MSYS2的安装，会出现选项123，选3就行。这个过程会下载很多安装包什么的，耐心等待，一定要耐心，要完整装完才行，装好会让你再做一次123选择，这个时候不需要选了，直接enter退出就行了。

## 3.  安装bundler
输入
```
gem install bundler
```
执行安装

## 4.  安装jekyll
输入命令：
```
gem install jekyll
```
检查jekyll是否安装成功
```
jekyll -v
```
如果没什么问题，会显示版本信息说明安装成功。

英文好的童鞋直接参考[jekyll官方文档](https://jekyllrb.com/docs/installation/windows/)

## 5.  使用jekyll创建简单的博客
### 5.1 创建博客
输入命令：
```
jekyll new myblog
```
### 5.2 本地运行博客
切换到myblog目录下，输入如下命令
```
bundle exec jekyll serve
```
打开浏览器，访问http://localhost:4000,即可查看博客。