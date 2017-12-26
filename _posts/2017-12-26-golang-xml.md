---
bg: tools.jpg
layout: post
title: "golang xml解析不确定是否存在的元素"
crawlertitle: ""
summary: ""
date:   2017-08-29 15:41:43 +0800
categories: language go
tags: golang
author: qutao
---

golang中负责解析函数, 只能对struct, slice和string进行解析
{% highlight go %}
func Unmarshal(data []byte, v interface{}) error
{% endhighlight %}

golang中负责生成xml函数
{% highlight go %}
func Marshal(v interface{}) ([]byte, error)
{% endhighlight %}

Marshal可以处理指针指向的值, 若指针为nil, 则不会写入到xml中。

我们可以在定义解析的struct结构时, 将元素的类型定义为指针类型, 若解析时不存在该元素, 则在marshal时不会生成空的元素。
{% highlight go %}
type ObjectInfo struct {  
  // attr  
  ObjectType string `xml:"ObjectType,attr"`  
  Id string `xml:"Id,attr"`  
  Name string `xml:"Name,attr"`  
  // tag  
  Tag []TagInfo `xml:"TagCollection>Tag"`  
  // ColorValues  
  RefSpecData *ReflectanceSpectrumInfo `xml:"ColorValues>ReflectanceSpectrum"`  
  ColorLabData *ColorCIELabInfo `xml:"ColorValues>ColorCIELab"`  
  ColorDensityData *ColorDensityInfo `xml:"ColorValues>ColorDensity"`  
  //ColorValues PrintDataInterface `xml:",innerxml"`  
  // DeviceColorValues  
  ColorCMYKPlusNData *ColorCMYKPlusNInfo `xml:"DeviceColorValues>ColorCMYKPlusN"`  
  ColorCMYKData *ColorCMYKInfo `xml:"DeviceColorValues>ColorCMYK"`  
  //DeviceColorValues PrintDataInterface `xml:",innerxml"`  
}
{% endhighlight %}
上面结构是在解析CxF定义的结构, 扫描后生成的数据类型是不确定的, 但又是固定的几组结构, 指针类型能很好的解决这个问题。
