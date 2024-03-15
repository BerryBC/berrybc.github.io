---
layout: post
title:  拾柒-VBA 给 Excel Worksheet 上锁
date:   2019.12.16 23:10:55 +0800
categories: 三世书记录
author: 崔秉龙
location: Guangzhou, China
---



这个主要是记录给我为公司写的小宏，都什么年代了，还写 `VB宏` ，我觉得绝望。

主要在之前的基础上实现了两个功能：

# 1. 在查询数据的时候把数据内容 Sheet 显示

因为又要把整个 `Excel` 放在公共盘，又不能把内容直接给别人看，这是闹哪样？

不过还是这样吧，做了吧，当作是为了工资。

首先我有一个 `Sheet` 叫 `DBR` ，这个 `Sheet` 就是数据源，在平时要隐藏起来，而因为隐藏的时候是更新和读取不了数据的，所以要展开这个 `Sheet` ，这个好做，代码是：

```vb
Sub subHideDBR()
    Sheets("DBR").Select
    ActiveWindow.SelectedSheets.Visible = False

End Sub

Sub subShowDBR()
    Sheets("DBR").Visible = True
End Sub

```

完成，完事。

# 2.在平日要把 Sheet 上锁，用宏的时候打开

这个其实在集团下发的 `Sheet` 上面有这个功能，但因为集团的数据我不敢带回家（奶奶的，连集团都还是用 `Excel 宏`，有意义吗？什么年代了？），所以就找资料吧。

```vb

Const strBookPW As String = "Shit"
Const strSheetPW As String = "Damn"

'锁整个工作本'
Sub subLockBook()
    ActiveWorkbook.Protect strBookPW, Structure:=True, Windows:=False
End Sub

'解锁整个工作本'
Sub subUnLockBook()
    ActiveWorkbook.Unprotect strBookPW
End Sub

'锁 Sheet'
Sub subLockSheet()
    ActiveSheet.Protect DrawingObjects:=False, Contents:=True, Scenarios:= _
        False, AllowFormattingCells:=True, AllowFormattingColumns:=True, _
        AllowFormattingRows:=True, Password:=strSheetPW
End Sub

'解锁 Sheet'
Sub subUnLockSheet()
    ActiveSheet.Unprotect Password:=strSheetPW
End Sub
```

最后顺便在 Excel 工程中的 `ThisWorkbook` 对象内加入：

```vb
Private Sub Workbook_Open()
    Call subLockBook
    Sheets("Trend Input").Select
    Call subLockSheet
End Sub
```

方便在打开文件时对文件进行锁。