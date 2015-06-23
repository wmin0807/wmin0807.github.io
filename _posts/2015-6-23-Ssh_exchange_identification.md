---
layout: post
title: ssh_exchange_identification
---

在使用ssh user@ip连接远程主机的时候出现这个问题：

	ssh_exchange_identification: Connection closed by remote host 

针对这个问题做一下简单的总结：

1. 首先可以使用telent yourhost 22查看一下端口是否能通。
2. 首先怀疑是该机器out of memory，因为ssh 到一台机器的时候，这台机器会fork进程来完成这个操作，如果是系统的进程数太多超过限制，或者内存不足无法满足fork子进程操作所需要的资源，操作系统会主动断开链接。如果需要更多的信息可以使用ssh -v ...进行链接操作查看更多的错误信息。
3. 有可能是yourhost 机器下的/etc/hosts.allow文件不允许你的ip地址进行ssh登录。这个时候需要修改这个文件，具体的修改可以参考这个链接地址：

	**http://www.rjkfw.com/s_596.html**


