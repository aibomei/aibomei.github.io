---
bg: tools.jpg
layout: post
title:  "centos7 自定义服务"
crawlertitle: ""
summary: ""
date:   2017-12-22 14:25:30 +0800
categories: soft config
tags: 工具配置
author: qutao
---

1. 自定义服务配置管理目录*/usr/lib/systemd/system*下新建xxx.service文件
2. 我的文件名为iccserver.service, 内容如下:

    [Unit]

    Description="mticc server"

    After=rc-local.service

    [Service]

    Type=simple

    ExecStart=/etc/init.d/iccserver start

    ExecStop=/etc/init.d/iccserver stop

    PrivateTmp=True

    [Install]

    WantedBy=multi-user.target

    Type这个字段我设置为forking会报超时错误, ExecStart需使用绝对路径

3. systemd命令

    systemctl start xxx.service (不带后缀默认service)

    systemctl stop xxx.service

    systemctl daemon-reload (修改service后重载文件)

    systemctl status -l xxx.service (查看输出日志)
