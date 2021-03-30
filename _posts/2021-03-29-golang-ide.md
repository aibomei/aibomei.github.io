---
bg: tools.jpg
layout: post
title: "Golang配置IDE"
crawlertitle: ""
summary: ""
date:   2021-03-29
categories: markdown uml plantuml vscode
tags: golang
author: qutao
---

#遇到的问题
最近想在VSCode里配置Go的高亮和代码提示，需要安装gopls，故
> go get -v golang.org/x/tools/gopls

结果提示网络原因连接失败。网上找到一种解决方案，先下载源文件到本地，然后安装
> git clone https://github.com/golang/tools.git tools
git install github.com/golang/tools/gopls

问题又出现了，gopls的依赖库安装失败。git的SSL连接失败
> Fatal: fatal: unable to access 'https://github.com/xxx': OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443

可能github给GFW墙了。解决方法，git开启代理：
1. 酸酸乳需开始局域网本地代理(port:1080)
2. git config --global --add remote.origin.proxy "127.0.0.1:1080"

至此git clone正常，但是go get问题仍在。最后发现go也需开启代理
>go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct

Over，问题解决。

#Vim配置
1. 安装插件管理器*vim-pathogen*
2. 插件*vim-go*
3. 插件*vim-gocode*
4. 安装插件*tagbar*

##安装vim-pathogen
插件管理器需在vim目录下创建autoload和bundle目录，下载vim-pathogen到autoload目录下
> git clone https://github.com/tpope/vim-pathogen

然后在_vimrc编辑内容如下
>execute pathogen#infect()
syntax on
filetype plugin indent on

<span style="color:red;">之后如果想安装其他的插件，只需把插件放到bundle目录下即可</span>

##安装vim-go
语法高亮和代码检测
>进入bundle目录
git clone https://github.com/fatih/vim-go.git

##安装vim-gocode
代码提示插件，需先安装gocode
> go get github.com/nsf/gocode

然后再安装vim-gocode
> git clone https://github.com/Blackrush/vim-gocode.git

在_vimrc中设置gocode快捷键，比如F6，然后按F6就可以调出代码提示
> imap \<F6> \<C-x>\<C-o>

##安装tagbar
显示代码结构，首先需安装ctags，编译后的exe目录需加到path环境变量中
安装tagbar
> git clone https://github.com/majutsushi/tagbar.git

_vimrc加入如下行，然后按F8可打开关闭代码结构大纲
> nmap \<F8> :TagbarToggle\<CR>