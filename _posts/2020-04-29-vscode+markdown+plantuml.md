---
bg: tools.jpg
layout: post
title: "VSCode下使用Markdown+UML"
crawlertitle: ""
summary: ""
date:   2020-04-29
categories: markdown uml plantuml vscode
tags: 工具配置
author: qutao
---
# VSCode需要的插件
* PlantUML
> 可以绘制各种图形的脚本语言。需要安装java
* Markdown Preview Enhanced
> 可以对Markdown做增强预览,比如支持各种绘图等

# PlantUML依赖的工具
* graphviz
> 设置环境变量GRAPHVIZ_DOT：dot.exe可执行二进制文件的完整路径
* java
> 设置环境变量JAVA_HOME：Java SDK安装目录；环境变量Path加上%JAVA_HOME%\bin

# 预览
> 在Markdown Preview Enhanced中预览，右键可导出HTML或者PDF
