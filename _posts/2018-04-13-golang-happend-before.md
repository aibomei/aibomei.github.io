---
bg: tools.jpg
layout: post
title: "golang中的happens begore"
crawlertitle: ""
summary: ""
date:   2018-04-13 21:18:24 +0800
categories: language go
tags: golang
author: qutao
---

happens begore in golang
{% highlight go %}
var c = make(chan int) // unbuffered channel, read happens before write
//var c = make(chan int, 1) // buffered channel, write happens before read
// close channel happens before read???
var a string
func f() {
  a = "hello, world" // (1)
  <-c // (2)
}

func main() {
  go f()
  c <- 0 // (3)
  fmt.Println(a) // (4)
}
{% endhighlight %}

上例中，*unbuffered channel*能保证read操作发生在write之前，所以操作2发生在3之前，可以保证输出"hello, world"；

*buffered channel*write发生在read之前，所以操作3在2之前，输出结果为""。
