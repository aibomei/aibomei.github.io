---
bg: tools.jpg
layout: post
title:  "golang中的模板template"
crawlertitle: ""
summary: ""
date:   2017-01-10 21:30:42 +0800
categories: language go
tags: golang
author: qutao
---
## 示例代码
{% highlight go %}
package main
import "text/template"
import "os"
import "fmt"
import "strings"
type Friend struct {
    Fname string
}
type Person struct {
    UserName string
    Emails   []string
    Friends  []*Friend
}
// template function
func EmailDealWith(args ...interface{}) string {
    ok := false
    var s string
    if len(args) == 1 {
        s, ok = args[0].(string) // interface.(type) assert
    }
    if !ok {
        s = fmt.Sprint(args...)
    }
    // find the @ symbol
    substrs := strings.Split(s, "@")
    if len(substrs) != 2 {
        return s
    }
    // replace the @ by " at "
    return (substrs[0] + " at " + substrs[1])
}
// processed nest
func ProNest() {
  f1 := Friend{Fname: "minux.ma"}
  f2 := Friend{Fname: "xushiwei"}
  t := template.New("fieldname example")
  // type FuncMap map[string]interface{}
  t = t.Funcs(template.FuncMap{"EmailDeal": EmailDealWith})
  t, _ = t.Parse(`hello {{.UserName}}!
          {{range .Emails}}
              an email {{. | EmailDeal}}
          {{end}}
          {{with .Friends}}
          {{range .}}
              my friend name is {{.Fname}}
          {{end}}
          {{end}}
          {{with $x := "output" | printf "%q"}}{{$x}}{{end}}
          {{with $x := "output"}}{{printf "%q" $x}}{{end}}
          {{with $x := "output"}}{{$x | printf "%q"}}{{end}}
          `)
  p := Person{UserName: "Astaxie",
      Emails:  []string{"astaxie@beego.me", "astaxie@gmail.com"},
      Friends: []*Friend{&f1, &f2}}
  t.Execute(os.Stdout, p)
}
// processed condition
func ProCondition() {
  tEmpty := template.New("template test")
  tEmpty = template.Must(tEmpty.Parse("空 pipeline if demo: {{if ``}} 不会输出. {{end}}\n"))
  tEmpty.Execute(os.Stdout, nil)
  tWithValue := template.New("template test")
  tWithValue = template.Must(tWithValue.Parse("不为空的 pipeline if demo: {{if `anything`}} 我有内容，我会输出. {{end}}\n"))
  tWithValue.Execute(os.Stdout, nil)
  tIfElse := template.New("template test")
  tIfElse = template.Must(tIfElse.Parse("if-else demo: {{if `anything`}} if部分 {{else}} else部分.{{end}}\n"))
  tIfElse.Execute(os.Stdout, nil)
}
func main() {
   ProNest()
   ProCondition()
}
{% endhighlight %}

## 说明
1. {\{.\}}相当于c++中的this指针。如果要访问当前对象的字段通过{\{.FieldName\}},但是需要注意一点：这个字段必须是导出的(字段首字母必须是大写的)，否则在渲染的时候就会报错

2. {\{range\}}...{\{end\}}和{\{with\}}...{\{end\}}用于输出嵌套字段的内容
{\{range\}} 这个和Go语法里面的range类似，循环操作数据
{\{with\}}操作是指当前对象的值，类似上下文的概念

3. if...else...用于条件处理。注意：if里面无法使用条件判断，例如.Mail=="astaxie@gmail.com"，这样的判断是不正确的，if里面只能是bool值

4. {\{. \| string\}}会调用string关联的模板处理函数对输入数据进行过滤

5. 我们可以在一些操作中申明局部变量，例如with，range，if过程中申明局部变量，这个变量的作用域是{\{end\}}之前。方法：$variable := pipeline
