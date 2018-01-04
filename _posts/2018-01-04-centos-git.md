---
bg: tools.jpg
layout: post
title:  "Centos7下搭载Git服务器"
crawlertitle: ""
summary: ""
date:   2018-01-04 14:55:30 +0800
categories: soft config
tags: 工具配置
author: qutao
---

1. 安装git

    *yum install git*
2. 创建一个git用户, 运行git服务

    *adduser git*
3. 创建证书登录, 让团队成员有访问权

    具体过程请看我的另一篇[文章][1]。生成ssh公钥后, 将其导入到/home/git/.ssh/authorized_keys文件中(不存在文件则自己创建), 一行一个。
4. 准备git仓库

    创建git仓库目录, 假定为/home/sln/test.git。

    *mkdir test.git*

    *git init --bare test.git*

    *chown -R git:git test.git*
5. 禁止git帐户使用shell登录

    出于安全考虑, 可以使用git-shell工具限制git帐户的活动范围, 使其只能用ssh连接来推送和获取git仓库, 而不能直接登录shell。 通过修改/etc/password文件完成: 

    git:x:1003:1003::/home/git:/bin/bash 改为

    git:x:1003:1003::/home/git:/usr/bin/git-shell

Tips:
1. 本地push的时候提示需要输入密码。linux的/etc/ssh/sshd_config中加入

    PubkeyAuthentication yes

[1]: http://www.littlejing.com/soft/config/github-ssh/

