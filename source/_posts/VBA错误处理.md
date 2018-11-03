---
title: VBA错误处理
date: 2017-07-09 09:42:02
tags:
---
vba错误处理有三种方式:

---

1.执行标签行之后的代码
```
on error goto a

a: msgbox "没有要选择的工作表"
```
2.继续执行错误语句之后的代码
```
on error resume next
```
3.关闭错误捕捉
```
on error goto 0
```
