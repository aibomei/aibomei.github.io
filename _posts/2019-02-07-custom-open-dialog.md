---
bg: tools.jpg
layout: post
title: "Windows下定制打开对话框"
crawlertitle: ""
summary: ""
date:   2019-02-27 17:15:24 +0800
categories: winsows api custom system open dialog
tags: Utility
author: qutao
---
用于打开系统Open Dialog的函数为*[GetOpenFileName]*
{% highlight c%}
BOOL GetOpenFileNameA(
  LPOPENFILENAMEA Arg1
);
{% endhighlight %}

如果需要在对话框上定制内容，则需要为*OPENFILENAME*结构的*lpfnHook*赋值。一般也需要对*lpTemplateName*赋值，因为需要引用到定义的资源。*lCustData*是与对话框通信的结构。*Flags*需要加上
{% highlight c%}
OFN_EXPLORER | OFN_ENABLETEMPLATE | OFN_ENABLEHOOK
{% endhighlight %}

[GetOpenFileName]: https://docs.microsoft.com/en-us/windows/desktop/api/commdlg/nf-commdlg-getopenfilenamea 
***

示例代码如下：
{% highlight c%}
BOOL _stdcall FileOpenDlgCheckBox(HWND hwnd, PTSTR pstrFileName, 
  PTSTR pstrTitleName, TCHAR *Filter, int *bCheck)
{
  FileInitialize(hwnd);
  ofn.hwndOwner         = hwnd;
  ofn.lpstrFile         = pstrFileName;
  ofn.lpstrTitle        = pstrTitleName;
  ofn.lpstrFilter       = Filter;
  ofn.Flags             =
    OFN_OVERWRITEPROMPT | OFN_PATHMUSTEXIST | 
    OFN_HIDEREADONLY | OFN_EXPLORER | OFN_ENABLETEMPLATE | OFN_ENABLEHOOK;
  LRESULT CALLBACK OFNCheckBox(HWND, UINT, WPARAM, LPARAM);
  ofn.lCustData         = (LPARAM)bCheck;
  ofn.lpfnHook          = (LPOFNHOOKPROC)OFNCheckBox;
  ofn.lpTemplateName    = MAKEINTRESOURCE(IDD_LITTLE_UV);
  return GetOpenFileName(&ofn);
}

static void _stdcall GetCbSpace(HWND hDlg, int *W, int *H, int *X, int *Y)
{
  HWND hParent = GetParent(hDlg);
  // 1, 2为打开和取消Button的资源ID，可用Spy++查看
  HWND hBtnO = GetDlgItem(hParent, 1);
  HWND hBtnC = GetDlgItem(hParent, 2);

  RECT Ro, Rc, Rd;
  GetWindowRect(hBtnO, &Ro);
  GetWindowRect(hBtnC, &Rc);
  GetWindowRect(hDlg, &Rd);

  int Margin = Rc.top - Ro.top;
  *W = Rc.right - Rc.left;
  *H = Rc.bottom - Rc.top;
  *X = Ro.left - Rd.left; // dialog template relative position
  *Y = Ro.top - Rd.top + Margin + *H;
}

LRESULT CALLBACK OFNCheckBox(HWND hDlg, UINT UMessage, WPARAM WParam, LPARAM LParam)
{
  static HWND hCb;
  static int *pData;
  switch(UMessage)
  {
  case WM_INITDIALOG:
    {
      LPOPENFILENAME Ofn = (LPOPENFILENAME)LParam;
      pData = (int*)Ofn->lCustData;
      SendDlgItemMessage(hDlg, 3000, BM_SETCHECK, *pData, 0);
      hCb = GetDlgItem(hDlg, 3000);
      return 0;
    }
  case WM_SIZE:
    {
      int X, Y, W, H;
      GetCbSpace(hDlg, &W, &H, &X, &Y);
      SetWindowPos(hCb, NULL, X, Y, W, H, SWP_NOZORDER);
      return 0;
    }
  case WM_COMMAND:
    switch(HIWORD(WParam))
    {
    case BN_CLICKED:
      {
        if(LOWORD(WParam) == 3000)
        {
          int bCheck =
            SendDlgItemMessage(hDlg, 3000, BM_GETCHECK, 0, 0);
          
          *pData = bCheck;
        }
        return 1;
      }
    }
    return 0;
  }
  return 0;
}
{% endhighlight %}
