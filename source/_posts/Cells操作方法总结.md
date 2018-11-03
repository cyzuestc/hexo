---
title: Cells操作方法总结
date: 2017-08-01 09:42:02
tags:
---
1.
```
ActiveSheet.Cells(3,4).value = 20
```
2.
```
//单元格区域内第2行与第3列相交的单元格
Range("B3:F9").cells(2,3) = 100
```
3.
```
//引用整行
ActiveSheet.Rows("3:5").select
```
4.
```
//引用整列
ActiveSheet.Columns("F:G")
```
5.
```
//range对象的offset属性,向下2行向右3列
Range("A1").offset(2,3).value = 30
```
6.
```
//Range对象的CurrentRegion属性
Range("B5").CurrentRegion.Select
```
7.
```
//Range对象的End属性
Range("C5").End(xlUp).select
//xlDown,xlUp,xlTOLeft,xlToRight
```
8.
```
//常用技巧
ActiveSheet.Range("A65536").End(xlIup).offset(1,0).value = "张青"
```
9.
```
//with的用法
    With Range("A1:L1").Font
        .name = "宋体"
        .size = 12
        .color = RGB(255,0,0)
        .Bold = true
        .Italic = true
        .underline = xlUnderlinestyledouble
        .interior.color = RGB(255,255,0)
    endwith    
```
10.
```
//打开自动运行
private sub workbook_open()
    msgbox "你好"
end sub
```
11.
```
//关闭对话框提示
Application.DisplayAlerts = false
```
12.
```
//判断元素是否在字典中
dim dic as object
set dic=createobject("scriptng.dictionary")
'先添加元素
dic("XX")=""
…… 
判断元素
if dic.exists("XX")=true then msgbox "XX存在"
```
13
