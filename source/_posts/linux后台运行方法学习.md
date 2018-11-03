---
title: linux后台运行方法学习
date: 2018-04-16 11:59:38
tags:
---
# 前言
我们经常会碰到这样的问题,用telnet/ssh登陆了远程的linux服务器,运行了有些耗时较长的任务, 结果由于本地关闭终端窗口/网络断开而断开连接. 

## 场景:
如果只是临时有个命令需要长时间运行,什么方法能最简便的保证它在后台稳定运行?
## 解决办法:
当用户注销(logout)或者网络断开时, 终端会收到HUP(hangup)信号从而关闭其所有子程序.因此,我们的解决办法就有两种途径:要么让进程忽略hup信号,要么让进程运行再新的会话里,从而不属于此终端的子进程.

# 1.nohup
## 语法
nohup的用途就是让提交的命令忽略hangup信号.(run a command immune to hangups, with output to a non-tty).
nohup 的使用是十分方便的，只需在要处理的命令前加上 nohup 即可，标准输出和标准错误缺省会被重定向到 nohup.out 文件中。一般我们可在结尾加上"&"来将命令同时放入后台运行,也可用">filename 2>&1"来更改缺省的重定向文件名。
```
语法：nohup Command [ Arg … ] [　& ]

　　无论是否将 nohup 命令的输出重定向到终端，输出都将附加到当前目录的 nohup.out 文件中。

　　如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。

　　如果没有文件能创建或打开以用于追加，那么 Command 参数指定的命令不可调用。
```
## 实例
```
nohup /usr/local/node/bin/node /www/im/chat.js > /usr/local/node/output.log 2>&1 &
或者(这个操作很关键)
nohup command > myout.file 2>&1 &
tailf myout.file
```
## 其他命令
```
jobs -l 			只看当前终端.
ps -ef|grep java 查看进程
lsof -i:8080     查看使用某个端口的进程
netstat -ap|grep 8090  查看使用某个端口的进程
kill -9  进程号   终止后台进程
tailf myout.file 等同于tail -f -n 10（貌似tail -f或-F默认也是打印最后10行，然后追踪文件），与tail -f不同的是，如果文件不增长，它不会去访问磁盘文件
```
# setsid
nohup 无疑能通过忽略 HUP 信号来使我们的进程避免中途被中断，但如果我们换个角度思考，如果我们的进程不属于接受 HUP 信号的终端的子进程，那么自然也就不会受到 HUP 信号的影响了。setsid 就能帮助我们做到这一点。让我们先来看一下 setsid 的帮助信息：
```
SETSID(8)                 Linux Programmer’s Manual(手册,指南)              SETSID(8)
 
NAME
       setsid - run a program in a new session
 
SYNOPSIS
       setsid program [ arg ... ]
 
DESCRIPTION
       setsid runs a program in a new session.
```
可见 setsid 的使用也是非常方便的，也只需在要处理的命令前加上 setsid 即可。
setsid实例
```
[root@pvcent107 ~]# setsid ping www.ibm.com
[root@pvcent107 ~]# ps -ef |grep www.ibm.com
root     31094     1  0 07:28 ?        00:00:00 ping www.ibm.com
root     31102 29217  0 07:29 pts/4    00:00:00 grep www.ibm.com
```
值得注意的是，上例中我们的进程 ID(PID)为31094，而它的父 ID（PPID）为1（即为 init 进程 ID），并不是当前终端的进程 ID。请将此例与nohup 例中的父 ID 做比较。
# &
这里还有一个关于 subshell 的小技巧。我们知道，将一个或多个命名包含在“()”中就能让这些命令在子 shell 中运行中，从而扩展出很多有趣的功能，我们现在要讨论的就是其中之一。
当我们将"&"也放入“()”内之后，我们就会发现所提交的作业并不在作业列表中，也就是说，是无法通过jobs来查看的。让我们来看看为什么这样就能躲过 HUP 信号的影响吧。
```
[root@pvcent107 ~]# (ping www.ibm.com &)
[root@pvcent107 ~]# ps -ef |grep www.ibm.com
root     16270     1  0 14:13 pts/4    00:00:00 ping www.ibm.com
root     16278 15362  0 14:13 pts/4    00:00:00 grep www.ibm.com
[root@pvcent107 ~]#
```
从上例中可以看出，新提交的进程的父 ID（PPID）为1（init 进程的 PID），并不是当前终端的进程 ID。因此并不属于当前终端的子进程，从而也就不会受到当前终端的 HUP 信号的影响了。
# disown
## 场景
我们已经知道，如果事先在命令前加上 nohup 或者 setsid 就可以避免 HUP 信号的影响。但是如果我们未加任何处理就已经提交了命令，该如何补救才能让它避免 HUP 信号的影响呢？ 
## 解决方法
这时想加 nohup 或者 setsid 已经为时已晚，只能通过作业调度和 disown 来解决这个问题了。让我们来看一下 disown 的帮助信息： 
```
disown [-ar] [-h] [jobspec ...]
    Without options, each jobspec is  removed  from  the  table  of
    active  jobs.   If  the -h option is given, each jobspec is not
    removed from the table, but is marked so  that  SIGHUP  is  not
    sent  to the job if the shell receives a SIGHUP.  If no jobspec
    is present, and neither the -a nor the -r option  is  supplied,
    the  current  job  is  used.  If no jobspec is supplied, the -a
    option means to remove or mark all jobs; the -r option  without
    a  jobspec  argument  restricts operation to running jobs.  The
    return value is 0 unless a jobspec does  not  specify  a  valid
    job.

```
可以看出:
1. 用disown -h jobspec来使某个作业忽略HUP信号。
2. 用disown -ah 来使所有的作业都忽略HUP信号。
3. 用disown -rh 来使正在运行的作业忽略HUP信号。
需要注意的是，当使用过 disown 之后，会将把目标作业从作业列表中移除，我们将不能再使用jobs来查看它，但是依然能够用ps -ef查找到它。





