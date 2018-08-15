---
bg: tools.jpg
layout: post
title: "windows下判断模块是32位还是64位"
crawlertitle: ""
summary: ""
date:   2018-08-15 15:57:24 +0800
categories: winsows module 32 64
tags: Utility
author: qutao
---
首先介绍PE结构：

Windows系统下的可执行文件，是基于Microsoft设计的一种新的文件结构，此结构被称之为PE结构。PE的意思是Portable Executable(可移植的执行体)，所有Win32执行体都是用PE文件格式，其中包括SYS、DLL、EXE、COM、OCX等。（不管是学习逆向、破解还是安全，了解PE文件格式都是非常必要的。）

PE文件的第一个部分是IMAGE_DOS_HEADER，大小为64B，这里面有两个重要的数据成员。第一个为e_magic，这个必须为MZ，即0x5A4D。当然，0x5A4D这是典型的小端格式（Little Endian）；另一个重要的数据成员是最后一个成员e_lfanew，这个成员的值为IMAGE_NT_HEADERS的偏移。在IMAGE_DOS_HEADER和IMAGE_NT_HEADERS之间一个DOS Stub，这段程序用于在DOS环境中显示一个字符串，“This program cannot be run in DOS mode”，现在DOS早已灭绝，这段数据由编译器生成，可以用0填充或者删除（删除的话要移动很多数据）。

IMAGE_DOS_HEADER的定义如下：

{% highlight c%}
IMAGE_DOS_HEADER STRUCT
{//（注：最左边是文件头的偏移量。）
+0h  WORD e_magic		//Magic DOS signature MZ(4Dh 5Ah)		      DOS可执行文件标记
+2h  WORD e_cblp		//Bytes on last page of file  
+4h  WORD e_cp			//Pages in file
+6h  WORD e_crlc		//Relocations
+8h  WORD e_cparhdr	        //Size of header in paragraphs
+0ah WORD e_minalloc            //Minimun extra paragraphs needs
+0ch WORD e_maxalloc		//Maximun extra paragraphs needs
+0eh WORD e_ss			//intial(relative)SS value				DOS代码的初始化堆栈SS
+10h WORD e_sp			//intial SP value					DOS代码的初始化堆栈指针SP
+12h WORD e_csum		//Checksum
+14h WORD e_ip			//intial IP value					DOS代码的初始化指令入口[指针IP]
+16h WORD e_cs			//intial(relative)CS value				DOS代码的初始堆栈入口
+18h WORD e_lfarlc		//File Address of relocation table
+1ah WORD e_ovno		//Overlay number
+1ch WORD e_res[4]		//Reserved words
+24h WORD e_oemid		//OEM identifier(for e_oeminfo)
+26h WORD e_oeminfo	        //OEM information;e_oemid specific 
+29h WORD e_res2[10]		// Reserved words
+3ch DWORD e_lfanew             //Offset to start of PE header			         指向PE文件头
} IMAGE_DOS_HEADER ENDS
{% endhighlight %}

IMAGE_NT_HEADERS 结构的定义：
{% highlight c%}
IMAGE_NT_HEADERS STRUCT  
{  
+0h  DWORD  Signature; 
+4h  IMAGE_FILE_HEADER  FileHeader; 
+18h IMAGE_OPTIONAL_HEADER32  OptionalHeader; 
} IMAGE_NT_HEADERS ENDS
{% endhighlight %}

Signature 字段：在一个有效的 PE 文件里，Signature 字段被设置为00004550h,ASCII 码字符是“PE00”。标志这 PE 文件头的开始。“PE00” 字符串是 PE 文件头的开始，DOS 头部的 e_lfanew 字段正是指向这里。

IMAGE_FILE_HEADER 结构定义：
{% highlight c%}
typedef struct _IMAGE_FILE_HEADER  
{ 
+04h  // 运行平台 
+06h  WORD   NumberOfSections; // 文件的区块数目 
+08h  DWORD TimeDateStamp; // 文件创建日期和时间 
+0Ch  DWORD PointerToSymbolTable;// 指向符号表(主要用于调试) 
+10h  DWORD NumberOfSymbols; // 符号表中符号个数(同上) 
+14h  WORD   SizeOfOptionalHeader; // IMAGE_OPTIONAL_HEADER32 结构大小 
+16h  WORD   Characteristics;         // 文件属性 
} IMAGE_FILE_HEADER,  *PIMAGE_FILE_HEADER; 
{% endhighlight %}

* Machine：可执行文件的目标CPU类型。 

  IMAGE_FILE_MACHINE_I386 0x014c  x86 

  IMAGE_FILE_MACHINE_IA64 0x0200  Intel Itanium 

  IMAGE_FILE_MACHINE_AMD64 0x8664 x64 
* NumberOfSection: 区块的数目。（注：区块表是紧跟在 IMAGE_NT_HEADERS 后边的） 
* TimeDataStamp: 表明文件是何时被创建的。 
这个值是自1970年1月1日以来用格林威治时间（GMT）计算的秒数，这个值是比文件系统（FILESYSTEM）的日期时间更加精确的指示器。 
提示：VC的话可以用_ctime 函数或者 gmtime 函数。 
* PointerToSymbolTable: COFF 符号表的文件偏移位置，现在基本没用了。 
* NumberOfSymbols: 如果有COFF 符号表，它代表其中的符号数目，COFF符号是一个大小固定的结构，如果想找到COFF 符号表的结束位置，则需要这个变量。 
* SizeOfOptionalHeader: 紧跟着IMAGE_FILE_HEADER 后边的数据结构（IMAGE_OPTIONAL_HEADER）的大小。(对于32位PE文件，这个值通常是00E0h；对于64位PE32+文件，这个值是00F0h )。 
* Characteristics: 文件属性，有选择的通过几个值可以运算得到。( 这些标志的有效值是定义于 winnt.h 内的 IMAGE_FILE_** 的值，具体含义见下表。普通的EXE文件这个字段的值一般是 0100h，DLL文件这个字段的值一般是 210Eh。) 

***

示例代码如下：
{% highlight c%}
#include <windows.h>

static int CrnGetImageFileMachine(const char* lpFileName)
{
  IMAGE_DOS_HEADER idh;
  FILE *f = fopen(lpFileName, "rb");
  fread(&idh, sizeof(idh), 1, f);
  IMAGE_FILE_HEADER ifh;
  fseek(f, idh.e_lfanew + 4, SEEK_SET);
  fread(&ifh, sizeof(ifh), 1, f);
  fclose(f);

  return ifh.Machine;
}

static int IsProcess64(const char* fileName)
{
  WORD n = CrnGetImageFileMachine(fileName);
  switch(n)
  {
  case 0x014c: // x86
    return 0;
  case 0x8664: // x64
    return 1;
  case 0x0200: // Intel Itanium, ia-64
    return 0;
  default:
    return 0;
  }

  return 0;
}
{% endhighlight %}
