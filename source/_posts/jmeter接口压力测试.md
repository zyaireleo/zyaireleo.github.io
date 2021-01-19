---
title: jmeter接口压力测试
date: 2020-12-16 09:13:02
tags: 测试
---

### GetStarted

#### 常用压测工具

- loadrunner

```
性能稳定，压测结果及细粒度大，可以自定义脚本进行压测，但是太过于重大，功能比较繁多
```

- apache ab(单接口压测最方便)

```
模拟多线程并发请求,ab命令对发出负载的计算机要求很低，既不会占用很多CPU，也不会占用太多的内存，但却会给目标服务器造成巨大的负载, 简单DDOS攻击等
```

- webbench

```
webbench首先fork出多个子进程，每个子进程都循环做web访问测试。子进程把访问的结果通过pipe告诉父进程，父进程做最终的统计结果。
```

#### jmeter基本介绍

- 压测不同的协议和应用

```
1) Web - HTTP, HTTPS (Java, NodeJS, PHP, ASP.NET, …)
2) SOAP / REST Webservices
3) FTP
4) Database via JDBC
5) LDAP  轻量目录访问协议
6) Message-oriented middleware (MOM) via JMS
 7) Mail - SMTP(S), POP3(S) and IMAP(S)
8) TCP等等
```

- 使用场景及优点

```
1）功能测试
2）压力测试
3）分布式压力测试
4）纯java开发
5）上手容易，高性能
4）提供测试数据分析
5）各种报表数据图形展示
```