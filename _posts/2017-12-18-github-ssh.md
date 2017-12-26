---
bg: tools.jpg
layout: post
title:  "github验证从https到ssh"
crawlertitle: ""
summary: ""
date:   2017-12-18 10:18:30 +0800
categories: soft config
tags: 工具配置
author: qutao
---
# 通过SSH连接github
注意以下命令均在Git Bash下完成
1.  生成ssh keys
 >生成方式*ssh-keygen -t rsa -C <your_email@example.com>*,
 >默认路径是*c:/user/username/.ssh*, 默认文件名是id_rsa. 有提示输入密码, 我们暂时不要密码, 所以一路回车, 最后会在路径下生成两个文件, id_rsa和id_rsa.pub, pub是公钥, 是要发给外部的.
2.  把SSH key加到ssh-agent
	* 开启ssh-agent, Windows下面命令是*eval $(ssh-agent -s)*, 成功会提示”Agent pid id”
	* 把私钥id_rsa加入到ssh-agent, 命令如下*ssh-add ~/.ssh/id_rsa*, 后面跟文件路径.
	* 把公钥pub文件复制粘贴加入到github个人帐号
3.  测试SSH连接, 命令: *ssh –T git@github.com*
>出现 “hi, username! You’ve…” 即表示成功; 出现 “Error: Permission denied (publickey)”表示失败, 可能原因公钥不对.
4.  c:/user/uername/.ssh/config中添加配置(用于push commit), 我的如下:

    Host github.com

    HostName 192.30.255.112

    User qutao

    IdentityFile  ~/.ssh/id_rsa
5.  使用`git clone git@github.com:username/repository`克隆项目
