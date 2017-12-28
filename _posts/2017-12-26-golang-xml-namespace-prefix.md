---
bg: tools.jpg
layout: post
title: "golang解析xml namespace prefix的bug"
crawlertitle: ""
summary: ""
date:   2017-08-30 16:00:00 +0800
categories: language go
tags: golang
author: qutao
---

[https://github.com/golang/go/issues/11496][1]

示例使用链接中的示例, 我遇到的问题跟这个一样。
{% highlight go %}
<person xmlns:ns="ns">  
  <ns:name>Oliver</ns:name>  
  <ns:phone>110</ns:phone>  
</person>
{% endhighlight %}

解析结构体定义如下:
{% highlight go %}
type Person struct {  
    XMLName xml.Name `xml:"ns person"`  
    Name    string   `xml:"ns name"`  
    Phone   string   `xml:"ns phone"`  
}
{% endhighlight %}

Marshal后结果如下,缺少namespace *ns*
{% highlight go %}
<person xmlns="ns">  
  <name xmlns="ns">Oliver</name>  
  <phone xmlns="ns">110</phone>  
</person>
{% endhighlight %}

{% highlight go %}
UnMarshal:
type Person struct {  
    XMLName xml.Name `xml:"person"`  
  
    Name    string   `xml:"name"`  
    Phone   string   `xml:"phone"`  
}

Marshal:
type Person2 struct {  
    XMLName xml.Name `xml:"ns:person"`  
    NS string `xml:"xmlns:ns,attr"`  
  
    Name    string   `xml:"ns:name"`  
    Phone   string   `xml:"ns:phone"`  
}  
{% endhighlight %}
NS变量在调用Marshal前赋值, 然后把Person的数据拷贝到Person2中即可, 十分麻烦。

[1]: https://github.com/golang/go/issues/11496
