---
bg: tools.jpg
layout: post
title: "win32中的系统对话框多选文件获取文件路径"
crawlertitle: ""
summary: ""
date:   2018-07-26 13:44:24 +0800
categories: win32 api
tags: Utility
author: qutao
---

使用到结构体`OPENFILENAME`和函数`GetOpenFileName`。注意多选文件需要有`OFN_ALLOWMULTISELECT`标记。

{% highlight c%}
static BOOL OpenFile(HWND h, char *pp[], int *n)
{
  const char *Filter = "ICC File(*.icc;*.icm)\0*.icc;*.icm\0Data File(*.dat)\0*.dat\0All File(*.icc;*.icm;*.dat)\0*.icc;*.icm;*.dat\0\0";
  const char *Suffix = "icc;icm";
  TCHAR FN[1000]="";
  OPENFILENAME Op;
  // Initialize OPENFILENAME
  ZeroMemory(&Op, sizeof(Op));
  Op.lStructSize = sizeof(Op);
  Op.hwndOwner = h;
  Op.hInstance = GetModuleHandle(NULL);
  Op.lpstrFile = FN;
  Op.lpstrFile[0] = '\0';
  Op.nMaxFile = sizeof(FN);
  Op.lpstrFilter = Filter;
  Op.nFilterIndex = 1;
  Op.lpstrDefExt = Suffix;
  Op.Flags = OFN_OVERWRITEPROMPT | OFN_ENABLESIZING | OFN_PATHMUSTEXIST | 
    OFN_HIDEREADONLY | OFN_EXPLORER | OFN_ENABLETEMPLATE | OFN_ALLOWMULTISELECT;
  //Op.lpfnHook = OFNHookProc;
  //Op.lCustData = (LPARAM)&Fp;
  if(!GetOpenFileName(&Op))
    return FALSE;

  // get select path
  char Path[0xff];
  memcpy(Path, FN, Op.nFileOffset);
  Path[Op.nFileOffset] = 0;
  if(Path[Op.nFileOffset-1] != '\\' && Path[Op.nFileOffset-1] != '/')
    Path[Op.nFileOffset-1] = '\\';
  const char *p = FN + Op.nFileOffset;
  *n = 0;
  while(*p && *n < 3)
  {
    strcpy(pp[*n], Path);
    strcat(pp[*n], p);
    *n += 1;
    p += strlen(p) + 1;
  }
  return TRUE;
}
{% endhighlight %}
***

[nFileOffset]

指定从路径的开头到lpstrFile指向的字符串中的文件名的从零开始的偏移量（以字节为单位（ANSI版本）或16位字符（Unicode版本））。例如，如果lpstrFile指向以下字符串c:\dir1\dir2\file.ext，则此成员包含值13以指示file.ext字符串的偏移量。
>Specifies the zero-based offset, in bytes (ANSI version) or 16-bit characters (Unicode version), from the beginning of the path to the file name in the string pointed to by lpstrFile. For example, if lpstrFile points to the following string, c:\dir1\dir2\file.ext, this member contains the value 13 to indicate the offset of the file.ext string.

[nFileOffset]: https://msdn.microsoft.com/en-us/library/ms961284.aspx "msdn"
