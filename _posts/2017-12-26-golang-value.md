---
bg: tools.jpg
layout: post
title: "golang声明变量"
crawlertitle: ""
summary: ""
date:   2015-12-13 19:38:31 +0800
categories: language go
tags: golang
author: qutao
---
{% highlight go %}
var v1 int  
var v2 string  
var v3 [10]int // 数组  
var v4 []int // 数组切片, v3[m:n](0 <= m <= n, n <= len(v3))  
var v5 struct {  
f int  
}  
var v6 *int // 指针  
var v7 map[string]int // map， key为string类型， value为int类型  
var v8 func(a int) int
var v9 chan interface{} // channel, make(chan interface{}), make为channel, map, slice内置类型赋值
{% endhighlight %}
