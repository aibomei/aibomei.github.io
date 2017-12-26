---
bg: tools.jpg
layout: post
title: "struct匿名字段,interface和断言"
crawlertitle: ""
summary: ""
date:   2016-09-06 22:39:52 +0800
categories: language go
tags: golang
author: qutao
---

{% highlight go %}
// 定义interface  
type Men interface {  
SayHi()  
Sing(lyrics string)  
}
 
type Human struct {  
name string  
age int  
phone string  
}
type Student struct {  
Human //匿名字段  
school string  
loan float32  
}  
//Human实现SayHi方法  
func (h Human) SayHi() {  
fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)  
}
//Human实现Sing方法  
func (h Human) Sing(lyrics string) {  
fmt.Println("La la la la...", lyrics)  
}   
//Employee重载Human的SayHi方法  
func (e Employee) SayHi() {  
fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,  
e.company, e.phone)  
}
{% endhighlight %}

Interface Men被Human,Student和Employee实现，
因为这三个类型都实现了这两个方法，Interface Men的值可以是实现了该接口的任一对象。任何对象只要定义了String方法，都可以用Printf输出函数输出，如下：
{% highlight go %}
type Element interface{}  
type List [] Element  
type Person struct {  
name string  
age int  
}

//定义了String方法
func (p Person) String() string {  
return "(name: " + p.name + " - age: "+strconv.Itoa(p.age)+ " years)"  
}

func main() {  
list := make(List, 3)  
list[0] = 1 // an int  
list[1] = "Hello" // a string  
list[2] = Person{"Dennis", 70}

// Go语言里面有一个语法，可以直接判断某个接口是否是该类型的变量(断言assert interface.(type))
// value, ok = element.(T)  
for index, element := range list {  
if value, ok := element.(int); ok {  
fmt.Printf("list[%d] is an int and its value is %d\n", index, value)  
} else if value, ok := element.(string); ok {  
fmt.Printf("list[%d] is a string and its value is %s\n", index, value)  
} else if value, ok := element.(Person); ok {  
fmt.Printf("list[%d] is a Person and its value is %s\n", index, value)  
} else {  
fmt.Printf("list[%d] is of a different type\n", index)  
}  
}  
}

//另外一种使用switch的用法:
for index, element := range list{  
switch value := element.(type) {  
case int:  
fmt.Printf("list[%d] is an int and its value is %d\n", index,  
case string:  
fmt.Printf("list[%d] is a string and its value is %s\n", inde  
case Person:  
fmt.Printf("list[%d] is a Person and its value is %s\n", inde  
default:  
fmt.Println("list[%d] is of a different type", index)  
}  
}
{% endhighlight %}
