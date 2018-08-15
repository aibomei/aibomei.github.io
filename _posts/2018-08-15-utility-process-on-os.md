---
bg: tools.jpg
layout: post
title: "windows下识别当前系统是否是32位还是64位"
crawlertitle: ""
summary: ""
date:   2018-08-15 16:50:24 +0800
categories: winsows module os 32 64
tags: Utility
author: qutao
---
1. `IsWow64Process`是Windows API提供的接口，用来判断指定的进程是否在WOW64下运行。
以下是WOW64的说明，摘自[MSDN]
>WOW64是x86仿真器，允许32位基于Windows的应用程序在64位Windows上无缝运行。这允许32位（x86）Windows应用程序在64位（x64）Windows中无缝运行，以及32位（x86）和32位（ARM）Windows应用程序无缝运行在64位（ ARM64）Windows。

2. _WIN64是一个预定义宏，当编译目标平台为64位时，此宏会被定义。

[MSDN]: https://docs.microsoft.com/zh-cn/windows/desktop/WinProg64/running-32-bit-applications
***

示例代码如下：
{% highlight c%}
#include <windows.h>

static BOOL IsWow64()  
{
  BOOL bIsWow64 = FALSE;

  //IsWow64Process is not available on all supported versions of Windows.  
  //Use GetModuleHandle to get a handle to the DLL that contains the function  
  //and GetProcAddress to get a pointer to the function if available.  
#ifdef _WIN64 // the process is compiled on 64 bit
  return TRUE;
#endif
  fnIsWow64Process = (LPFN_ISWOW64PROCESS) GetProcAddress(
    GetModuleHandle(TEXT("kernel32")),"IsWow64Process");

  if(NULL != fnIsWow64Process)
  {
    if (!fnIsWow64Process(GetCurrentProcess(),&bIsWow64))
    {
      //handle error
    }
  }
  return bIsWow64;
}
{% endhighlight %}
