---
bg: tools.jpg
layout: post
title: "golang加载css和js"
crawlertitle: ""
summary: ""
date:   2016-11-26 23:23:22 +0800
categories: language go
tags: golang
author: qutao
---
# 结构
└── main.go

└── static

└────   css

└── ── ── main.css

└── ── js

└── ── ── main.js

└── view

└── ── index.html

若有如上目录结构

# 代码
{% highlight go %}
func main() {
    http.Handle("/static/", http.StripPrefix("/static/", http.FileServer(http.Dir("static")))) // 启动静态文件服务
    // ...
}
{% endhighlight %}

{% highlight html %}
<!-- index.html -->
<link rel="stylesheet" href="/static/css/main.css" type="text/css">
<script type="text/javascript" src="/static/js/main.js"></script>
{% endhighlight %}

