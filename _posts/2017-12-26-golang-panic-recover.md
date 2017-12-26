---
bg: tools.jpg
layout: post
title: "golang错误处理之panic和recover"
crawlertitle: ""
summary: ""
date:   2015-12-13 21:46:24 +0800
categories: language go
tags: golang
author: qutao
---

panic()类似c++中的throw

recover()类似c++中的catch
{% highlight go %}
func CopyFile(SrcFileName, DstFileName string) (w int64, err error) {
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("Runtime error is %v\n", r)
		}
	}()

	SFile, e := os.Open(SrcFileName)
	if e != nil {
		panic("the src file is not exist...")
		return
	}
	defer SFile.Close()

	DFile, e := os.Create(DstFileName)
	if e != nil {
		panic("the dst file is not exist...")
		return
	}
	defer DFile.Close()

	// anonymous function
	/*
		defer func() {
			SFile.Close()
			DFile.Close()
		}
	*/
	return io.Copy(DFile, SFile)
}
{% endhighlight %}
