---
bg: tools.jpg
layout: post
title: "Unicode到Multi-byte的转换"
crawlertitle: ""
summary: ""
date:   2018-09-07 14:05:24 +0800
categories: winsows api unicode multi-byte
tags: Utility
author: qutao
---
需要使用到*[WideCharToMultiByte]*
{% highlight c%}
int WideCharToMultiByte(
  UINT                               CodePage,
  DWORD                              dwFlags,
  _In_NLS_string_(cchWideChar)LPCWCH lpWideCharStr,
  int                                cchWideChar,
  LPSTR                              lpMultiByteStr,
  int                                cbMultiByte,
  LPCCH                              lpDefaultChar,
  LPBOOL                             lpUsedDefaultChar
);
{% endhighlight %}

函数*fread fwrite*用来读写二进制数据, 函数*fgets*会把行末的换行符*\n*给读取进来。

[WideCharToMultiByte]: https://docs.microsoft.com/zh-cn/windows/desktop/api/stringapiset/nf-stringapiset-widechartomultibyte
***

示例代码如下：
{% highlight c%}
static int WideChar2TChar(wchar_t *wc, char *tc)
{
  DWORD dwNum = WideCharToMultiByte(CP_OEMCP,NULL,wc,-1,NULL,0,NULL,FALSE);
  char *TC = new char[dwNum];
  if(!TC)
    return 1;

  WideCharToMultiByte(CP_OEMCP,NULL,wc,-1,TC,dwNum,NULL,FALSE);
  memcpy(tc, TC, dwNum);
  tc[dwNum] = 0;
  delete []TC;
  return 0;
}

// trim the '\n'
static void delreturn(wchar_t *b, int n, FILE *f)
{
  if(fgetws(b, n, f) != NULL)
    b[wcslen(b) - 1] = 0; // trim
}
{% endhighlight %}
