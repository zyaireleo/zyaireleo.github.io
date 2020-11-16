---
title: Linux复习总结
date: 2020-11-05 08:55:08
tags:
---

#### 基础命令

重启主机：reboot
重启网卡：systemctl restart network.service
查看ip地址：ip addr
清屏:ctrl +l
虚拟机克隆后： systemctl restart network.service命令执行会报错，原因是MAC地址不正确，在网卡路径：/etc/sysconfig/network-scripts/ifcfg-enoxxxx修改MAC地址

#### linux核心操作

##### linux必备基础命令(一)

- cd命令
  功能说明：切换目录。
  举 例：cd /usr/local/；cd ..；cd -（返回上次路径）
- ls命令
  功能说明：列出目录内容。
  举 例：ls -ltr ；ls -lrt /home/
- pwd命令
  功能说明：查询所在目录。
  举 例： pwd
- cat命令
  功能说明：查看小文件内容。
  举 例：cat -n 123.txt
- more命令
  功能说明：查看大文件内容
  举 例：more System.map-3.10.0-123.el7.x86_64
- head命令
  功能说明：查看文件的前面N行。
  举 例：head -20 System.map-3.10.0-123.el7.x86_64
- tail命令
  功能说明：查看文件的后面N行。
  举 例：tail -f access.log ；tail -20 access.log
- touch命令
  功能说明：创建一个空文件。
  举 例：touch 123.txt
- mkdir命令
  功能说明：创建目录。
  举 例：mkdir -p /tmp/XD/XD/class
- rmdir命令
  功能说明：删除目录。
  举 例：rmdir /tmp/XD/XD/class
-  cp命令
  功能说明：拷贝文件。
  举 例：cp 123.txt class/ ； cp -a 123.txt class/789.txt
- mv命令
  功能说明：移动或更名现有的文件或目录。
  举 例：mv 123.txt 345.php ；mv 789.txt /home/987.php
-  rm命令
  功能说明：删除文件或目录。
  举 例：rm 987.php ；rm -rf 456.txt
-  diff命令
  功能说明：对比文件差异。
  举 例：diff 123.txt 456.txt
- sh命令
  功能说明：远程安全登录方式。
  举 例：ssh 192.168.226.131
- exit命令
  功能说明：退出命令。
  举 例：
- id命令
  功能说明：查看用户。
  举 例：id root
- uname命令
  功能说明：查询主机信息。
  举 例：uname -a
- ping命令

##### linux必备基础命令(二)

简介：讲解工作中常用的基础命令

- ping命令

  功能说明：查看网络是否通。
  举 例：ping 192.168.226.131

- echo命令
  功能说明：标准输出命令。
  举 例：echo "this is echo 命令"

- man命令(ls --help)
  功能说明：查看帮助文档
  举 例：man ls

- help命令
  功能说明：查看内部命令帮助
  举 例:help if

- clear命令
  功能说明：清屏。
  举 例：clear ; ctrl + l

- who命令
  功能说明：当前在本地系统上的所有用户的信息
  举 例：whoami ; who

- uptime命令
  功能说明：查询系统信息
  举 例：
  load average: 0.00, 0.01, 0.05 1分钟的负载，5分钟的负载，15分钟的负载

- w命令
  功能说明：查询系统信息
  举 例：w

- free命令
  功能说明：查看系统内存
  举 例：free -h ; free -m

- wc命令
  功能说明：统计行。
  举 例：wc -l 123.txt

- grep命令
  功能说明：查找文件里符合条件的字符串。
  举 例：grep '119.4.253.206' 123.txt | wc -l
  -n:输出行数 grep -n '80.82.70.187' 123.txt
  -w:精确匹配 grep -w '113.66.107.198' 123.txt
  -i:忽略大小写 grep -i 'IP:113.66.107.198' 123.txt
  -v:反向选择 grep -v '113.66.107.198' 123.txt

-  find命令
  功能说明：查询文件。
  举 例：find / -name -type f 123.txt

- uniq命令
  功能说明：对排序好的内容进行统计
  举 例：uniq -c 123.txt | sort -n

- sort命令
  功能说明：对内容进行排序
  举 例：uniq -c 123.txt | sort -n

- df命令
  功能说明：文件系统的磁盘使用情况统计。
  举 例：df -h

- netstat
  功能说明：查看网络端口的使用情况
  举 例：netstat -tunlp | grep nginx
  -t ：显示tcp端口
  -u ：显示UDP端口
  -n ：指明拒绝显示别名
  -l ：指明listen的
  -p ：指明显示建立相关连接的程序名
  安装netstat命令：yum -y install net-tools

- hostname命令
  功能说明：查看主机名
  举 例：hostname

- ps命令
  功能说明：显示所有进程信息。 ps 与grep 常用组合用法，查找特定进程
  举 例：ps -ef | grep nginx
  ps -aux | grep nginx

- kill命令
  功能说明：杀进程
  举 例： kill -9 top

- top命令
  功能说明：监控Linux系统状况，比如cpu、内存的使用
  举 例：按住键盘q退出

- du命令
  功能说明：统计大小
  举 例：du -sh ； du -sm *

- firewall-cmd命令
  功能说明：查看防火墙的状态
  举 例：firewall-cmd --state
  centos 7 关闭防火墙：systemctl stop firewalld.service

- echo命令
  功能说明：判断上一条命令是否正确
  举 例：echo $?

- cal命令
  功能说明：查看日历
  举 例：cal 2008

##### linux输入输出错误重定向

简介：介绍输入输出错误重定向的使用
- 输入重定向： <

  通俗的讲，输入重定向就是把要输入的信息写入到指定的文件中去

  eg：wc -l < 123.txt

- 输出重定向：> #代表覆盖写入 ； >> #代表追加写入

  通俗的讲，输出重定向就是把要输出的信息写入到一个文件中去，而不是将要输出的文件信息输

  eg： cat >> 123.txt ; cat > 123.txt ; ls -lrt >123.txt ; echo '123455' > 123.txt

- 错误重定向：

  通俗的讲，错误重定向就是把错误的信息写入到一个文件中去

   eg：llll 2> 123.txt ； llll 2> /dev/null #/dev/null 无底洞

- linux中一切皆文件

  ```
  posix名称 文件描述符 用途
  /dev/stdin 0 标准输入
  /dev/stdout 1 标准输出
  /dev/stderr 2 标准错误输出
  ```

- 几个符号：
  - & #代表等同于的 意思 ls -lrt /boot /test 1>/root/123.txt 2>&1
  - &> #代表不分正确还是错误的意思 ls -lrt /boot /test &>123.txt
  - | #管道符
  - ; #代表的是可以执行多条命令 cat /etc/passwd | grep root ; ls -lrt
  - && #前面的命令执行成功的话，后面的才可以执行成功；前面的命令执行失败的话，后面的不可以执行
  - || #前面的命令执行成功的话，后面的不可以执行；前面的命令执行失败的话，后面的可以执行

#### 常见目录

​	centos7：

- /：根目录，一般根目录下只存放目录，不要存放文件，也不要修改，或者删除目录下的内容
- /mnt：测试目录
- /root：root用户的家目录
- /home：普通用户的家目录
- /tmp：临时目录(比如文件上传时)
- /var：存放经常修改的数据，比如程序运行的日志文件
- /boot：存放的启动Linux 时使用的内核文件，包括连接文件以及镜像文件
- /etc：系统默认放置配置文件的地方
- /bin：所有用户都能执行的程序
- /sbin：只有root才能执行的程序
- /usr：用户自己的软件都可以放到这儿来
- /dev：存放硬件设备的地方(/dev/cdrom)
  /media：挂载光盘使用的
- 挂载光盘：mount /dev/cdrom /media

- 卸载光盘：umount /dev/cdrom
  绝对路径：说白了就是完整的路径

  相对路径：相对于当前位置路径 ./ 代表的是当前目录的意思 ../ 代表的是上一级目录的意思

#### 进阶操作

##### 虚拟机与外部物理机时间同步

- 卸载的光盘的时候：

  ```
  [root@localhost media]# umount /dev/cdrom
  umount: /media: target is busy.
  (In some cases useful info about processes that use
  the device is found by lsof(8) or fuser(1))
  ```

- 解决方法：

  - 首先确认联网状态
  - yum install -y psmisc
  - fuser -mv /media
  - fuser -kv /media

- date命令

  - date "+%Y-%m-%d %H:%M:%S"
  - date -s "2020-10-1 22:00:00"
  - date -d yesterday "+%Y-%m-%d %H:%M:%S"
  - date "+%w"

- 安装VMwareTools

  - 第一步打开虚拟机，安装VMwareTools使工具软件包下载到光盘
  - 挂载光盘到linux系统
  - cp VMwareTools-10.2.0-7259539.tar.gz /root/
  - umount /dev/cdrom
  - tar -xf VMwareTools-10.2.0-7259539.tar.gz
  - cd vmware-tools-distrib
  - yum -y install perl-Data-Dumper
  - ./vmware-install.pl
  - 一路按住键盘的 回车 键，选用默认
  - echo $? 验证是否安装成功，返回0就是成功
  - 验证里面虚拟机的时间是否与外部物理机的时间同步

##### vi编辑器

- vi的基本概念:（三种模式）

  1. 命令模式
  2. 插入模式
  3. 底行模式

  ```
  进入插入模式：按住键盘的 i 或者 o 或者 a
  进入命令模式：按住键盘的左上角esc键
  进入底行模式：前提是得在命令模式，输入 ： 进入
  ```

- 在命令行模式中的操作：

  ```
  $ #移动到这一行的行尾
  gg #移动到文档第一行行首
  G #移动到文档最后一行行首
  x #删除内容，删除一个字符
  dd #删除游标所在的那一整行
  u #复原原来的操作
  v #选中范围按y即复制
  p #粘贴
  ```

- 在底行模式中的操作：

  ```
  n #n为数字。光标移动到第n 行
  / #寻找内容
  %s/word1/word2/g #从第一行到最后一行寻找 word1 字符串，并将该字符串取代为 word2
  n1,n2s/word1/word2/g #n1 与 n2 为数字。在第 n1 与 n2 行之间寻找 word1 这个字符串，并将该字符串取代为 word2
  set nu #显示行号
  set nonu #取消行号
  q! #强制离开不保存
  wq #离开并保存
  wq! #强制离开并保存
  !ls #暂时离开
  ```

##### inux的用户管理与组管理

- linux用户的分类：

1. 超级用户root：拥有至高无上的权限 UID：0
2. 普通用户：权限有一定的限制，可以登录系统。一般可以执行/usr/local/bin或者/bin或者/usr/bin或者自己家目录的命令 UID：500 -60000 （centos 6） UID：1000 - 60000（centos7）
3. 系统用户（伪用户）：一般不会登录系统，一般情况是用来维持某个服务程序 UID ：1-499 （centos6）UID ：1-1000 （centos 7）

- 关于用户的相关配置文件

  - 账号信息：/etc/passwd

  - 密码信息：/etc/shadow

    ```
    test :x :1000 :1000 : :/home/test :/bin/bash
    用户 密码占位符 UID GID 用户描述 用户家目录 登录后使用的shell解释
    /sbin/nologin #是不可登录的
    /bin/bash #可以登录
    ```

- 添加用户命令：useradd

  - -u #指定用户UID

  - -d #指定用户主目录

  - -g #指定用户所属组

  - -r #指定用户是系统用户

  - -s #用户登录shell解释器

  - -M #不创建主目录
    eg：创建一个用户XD，指定UID为1010，指定家目录为/home/XD ,指定所属组为root组，指定登录shell为/bin/bash

    ```
    useradd -u 1010 -d /home/XD -g root -s /bin/bash XD
    ```

  - 登录用户时出现以下信息如何解决：

    ```
    如下：
    bash-4.2$
    bash-4.2$
    解决：复制相关信息到家目录
    cp -r /etc/skel/.bash* /home/XD/
    ```

- 删除用户命令：userdel

  - -r #连同家目录一块删除

- 添加用户组命令：groupadd

- 删除用户组命令：groupdel

- 修改用户的信息命令：usermod

  - -u #指定用户UID
  - -d #指定用户主目录
  - -g #指定用户所属组

- 设置用户密码命令passwd
  - passwd XD
  - echo "123456" | passwd --stdin XD

##### 文件属性与权限操作

- 文件的属性：ls -lrti

  ```
  135088935 -rw-------. 1 root root 1778 Oct 1 2020 yum.log第一列：i节点；i节点可以理解文件id，一个i节点号可以对应多个文件，一个文件只能对应一个i节点号
  第二列：文件的类型与权限
  
  - #代表的是文件；d#代表是目录； l #软链接文件 ；b #代表块设备；c #代表的是硬件设备（键盘）
    r：表示读权限 ；w：表示写权限；x：表示执行权限
    4：表示读权限 ；2：表示写权限；1：表示执行权限
    rw-------:分为三列 rw- --- ---，第一列为所属者的权限，第二列为所属组的权限，第三列为其它的权限
    第三列：有多少文件名链接到这个节点
    第四列：文件的所有者
    第五列：文件的所有组
    第六列：容量大小，单位默认为B
    第八列：创建或最近修改的时间
    第九列：文件名
  ```

- 链接

  ```
  软连接：ln -s
  eg：ln -s /home/XD/yum.log /usr/local/
  i节点号跟源文件不一样，源文件一旦删除，软链接将找不到源文件
  硬链接：ln
  eg：ln /home/XD/yum.log /usr/local/XD/
  i节点与源文件一模一样，源文件删除，硬链接还可以继续使用。常用于防止重要文件被误删
  ```

- 修改文件的权限命令chmod：

  - -R #递归的意思

  - chmod -R 777 /home/XD/*

    ```
    eg：
    chmod u+x,g+w,o+w boot.log
    chmod u-x,g-w,o-w boot.log
    chmod 777 boot.log
    ```

- 修改文件的所有者跟所属组命令chown：

  - -R #递归的意思

  ```
  eg：更改文件目录XD 的所属者为root用户 跟 所属组为XD组
  chown -R root:XD XD
  ```

##### 文件归档与解压缩

- 文件归档：

  ​	文件归档也称之为打包，指的是一个文件或者多个文件或者目录的一个集合，这个集合储存在一个文件中。归档文件是没有进行压缩的，所以占用的空间是所有文件或者目录的总和。工作中经常与压缩结合在一起使用

- 文件压缩：

  节约磁盘空间，加快文件的传输速率

###### 解压缩命令

- gzip：不能压缩目录，只能压缩文件，压缩速度最快，但是压缩比例比较低。扩展名：.gz
  - 不保留源文件压缩：gzip 123.txt
  - 保留源文件压缩：gzip -c 345.txt > 345.txt.gz
  - 不保留源文件的解压：gunzip 123.txt.gz
  - 保留原文件的解压：gunzip -c 345.txt.gz > 234.txt
  - 不保留源文件解压：gzip -d 345.txt.gz
- xz ：可以压缩目录和文件压缩的速度比较慢，但是压缩比例最高。扩展名：.xz
  - 不保留源文件压缩：xz 123.txt
  - 保留源文件压缩：xz -c 345.txt > 345.txt.xz
  - 不保留源文件的解压：unxz 345.txt.xz
  - 保留原文件的解压：xz -d -k 123.txt.xz
  - 不保留源文件解压：xz -d 123.txt.xz

###### 归档与压缩命令

​	tar：

- -c #创建新文件

- -f #指定文件格式

- -v #显示详细过程

  eg：

  - tar -cf vmware.tar vmware-tools-distrib；
  - tar -cvf vmware-tools.tar vmware-tools-distrib

- -z #以gzip方式归档压缩 

  eg：

  - tar -zcvf vmware-tools.tar.gz vmware-tools-distrib

- -J #以xz方式进行归档压缩

   eg：

  - tar -Jcvf vmware-tools.tar.xz vmware-tools-distrib；
  - tar -Jcvf /home/XD/vmware-tools.tar.xz vmware-tools-distrib

- -v #解档解压操作 

  - tar -xf vmware-tools.tar.xz

- -C #指定解压路径

##### find命令

###### 基本用法

find 路径 选项

- -type #根据文件类型 find /var/log -type f -name "*.log" ；

  find /var/log -type d

- *-name #根据文件名 find /var/log -type f -name "*.log"

- -perm #根据文件权限 find /var/log -perm 600 -type f -name "*.log"

-user #根据文件所属主 find /var/log -user XD

###### 高级用法

```
find /var/log -type f -name "*.log" -exec wc -l {} \;
; #可以执行多条命令
\ #转义符，转义;使得这条命令结束
{}#把find命令匹配到的每一次结果传递给{}
-exec #执行
eg：
find /var/log -type f -name "*.log" -exec cp -a {} /home/test \;
-mtime #根据文件的变更时间来查找；-n表示更改时间距离现在n天以内；+n表示更改时间距离现在n天以前
eg：
find /var/log -mtime -2 -name "*.log" -exec ls -lrt {} \;
find /var/log -mtime +2 -name "*.log" -exec ls -lrt {} \;
```

###### CentOS7的防火墙以及selinux

- 查看firewalld服务状态
  - systecmctl status firewalld
- 开启、重启、关闭firewalld服务
  - 开启：systemctl start firewalld.service
  - 关闭：systemctl stop firewalld.service
  - 重启：systemctl restart firewalld.service
- 查看firewall防火墙的状态
  - firewall-cmd --state
- 查看防火墙开放端口规则
  - firewall-cmd --list-port
- 开放80端口
  - firewall-cmd --permanent --add-port=80/tcp （--permanent永久生效，没有此参数重启后就失效）
- 加载生效开放的端口
  - firewall-cmd --reload
- 查询指定端口80是否开放
  - firewall-cmd --query-port=80/tcp
- 验证80端口是否开放：
  - 安装telnet命令：yum -y install xinetd telnet telnet-server （确认联网状态）
  - 安装netstat与ifconfig命令：yum -y install net-tools（确认联网状态）
- 关闭80端口
  - firewall-cmd --remove-port=80/tcp
- SELinux 的三种工作模式；配置文件路径：/etc/selinux/config
  - enforcing ：强制模式。违反selinux 规则的行为将会被阻止并记录到日志中去
  - permissive：宽容模式。违反selinux 规则的行为将会记录到日志中去
  - disabled：关闭模式。

###### 服务器之间telnet与scp命令的用法

- telnet命令：主要用于测试到某台机器的某个端口是否畅通

- telnet这个命令是依赖于 xinetd服务于telnet-server服务

- telnet命令的安装：yum -y install xinetd telnet telnet-server （确认联网状态）

- telnet命令用法：

  - telnet IP地址 端口
  - 应用场景：测试某个端口是否畅通

- scp命令：用于服务器之间的文件或者文件目录拷贝

  - 用法1：从本机拷贝文件到别的机器 scp 本机文件的存放路径 root@服务器IP:服务器目标路径

    ```
    scp /root/VMwareTools-10.2.0-7259539.tar.gz root@192.168.72.129:/root/
    ```

  - 用法2：从别的机器拷贝文件到本地目录 scp root@服务器IP:服务器目标路径 本机文件的存放路径

    ```
    scp root@192.168.72.129:/root/VMwareTools-10.2.0-7259539.tar.gz /root/
    ```

  - -r参数：递归的作用（可以拷贝目录）

    ```
    scp -r vmware-tools-distrib root@192.168.72.129:/root/
    ```

    

###### 进程管理命令之ps -ef与ps aux

- ps命令的参数作用

  ```
  [root@localhost ~]# ps -ef | more
  UID PID PPID C STIME TTY TIME CMD
  root 2 0 0 Jul30 ? 00:00:00 [kthreadd]
  root 3 2 0 Jul30 ? 00:00:06 [ksoftirqd/0]
  root 5 2 0 Jul30 ? 00:00:00 [kworker/0:0H]
  root 7 2 0 Jul30 ? 00:00:04 [migration/0]
  root 8 2 0 Jul30 ? 00:00:00 [rcu_bh]
  root 9 2 0 Jul30 ? 00:00:00 [rcuob/0]
  root 10 2 0 Jul30 ? 00:00:00 [rcuob/1]
  
  #UID：用户ID
  #PID：进程ID
  #PPID：父进程号
  #C：CPU的占用率
  #STIME：进程的启动时间
  #TTY：TTY终端
  #TIME：进程执行起到现在总的CPU占用时间
  #CMD：启动这个进程的命令
  ```

  ```
  [root@localhost ~]# ps aux | more
  USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
  root 2 0.0 0.0 0 0 ? S Jul30 0:00 [kthreadd]
  root 3 0.0 0.0 0 0 ? S Jul30 0:06 [ksoftirqd/0]
  root 5 0.0 0.0 0 0 ? S< Jul30 0:00 [kworker/0:0H]
  root 7 0.0 0.0 0 0 ? S Jul30 0:04 [migration/0]
  root 8 0.0 0.0 0 0 ? S Jul30 0:00 [rcu_bh]
  root 9 0.0 0.0 0 0 ? S Jul30 0:00 [rcuob/0]
  root 10 0.0 0.0 0 0 ? S Jul30 0:00 [rcuob/1]
  root 11 0.0 0.0 0 0 ? S Jul30 0:00 [rcuob/2]
  
  #USER：哪个用户启动了这个命令
  #PID：进程的ID
  #%CPU：CPU的占用率
  #%MEM：内存的使用率
  #VSZ：如果一个程序完全驻留在内存中一共需要使用多少内存空间
  #RSS：进程当前占用了多少内存
  #TTY：tty终端
  #STAT：表示当前进程的状态（S#处于休眠的状态；D#不可中断的状态 ；Z#僵尸进程 ；X#死掉的进程）
  #START：启动这个命令的时间点
  #TIME：进程执行起到现在总的CPU占用时间
  #COMMAND：启动这个进程的命令
  ```

  一般执行ps -ef 或者ps aux 命令是查看我们的进程是否启动成功，或者找出进程号，对进程的kill强制关闭

###### 处理海量数据之cut命令

cut应用场景：通常对数据进行列的提取
语法：cut [选项]...[file]

```
- 选项：
-d #指定分割符
-f #指定截取区域
-c #以字符为单位进行分割
注意：不加-d选项，默认为制表符，不是空格
eg:
以':'为分隔符，截取出/etc/passwd的第一列跟第三列
cut -d ':' -f 1,3 /etc/passwd
eg:
以':'为分隔符，截取出/etc/passwd的第一列到第三列
cut -d ':' -f 1-3 /etc/passwd
eg:
以':'为分隔符，截取出/etc/passwd的第二列到最后一列
cut -d ':' -f 2- /etc/passwd
eg:
截取/etc/passwd文件从第二个字符到第九个字符
cut -c 2-9 /etc/passwd
eg:
比如领导想叫你截取linux上面所有可登陆普通用户
cat /etc/passwd | grep '/bin/bash' | cut -d ':' -f 1 | grep -v root
```

###### 处理海量数据之awk命令

- awk的简介：一个非常强大的数据处理命令，支持条件判断，数组，循环等功能，与grep，sed被称为linux三剑客

- awk的应用场景：通常对数据进行列的提取

  - 语法：
    - awk '条件1 {执行动作} 条件2 {执行动作} ...' 文件名
    - 或awk [选项] '条件1 {执行动作} 条件2 {执行动作} ...' 文件名

- 特殊要点与举例说明:

  - printf #格式化输出，不会自动换行。

  - print #打印出内容，默认会自动换行

  - %s #代表字符串

  - \t #制表符

  - \n #换行符

    ```
    eg：printf '%s\t%s\t%s\t%s\t%s\t%s\n' 1 2 3 4 5 6
    ```

- awk的一些特殊要点与举例说明

  - NR #行号

  - $1 #代表第一列

  - $2 #代表第二列

  - $NF#代表最后一列

    ```
    df -h | awk 'NR==4 {print $1}'
    df -h | awk '(NR>=2 && NR <=5) {print $1}'
    df -h | awk '{print $NF}'
    ```

  - -F #指定分割符

    ```
    awk -F":" '{print $1}' /etc/passwd
    ```

  - BEGIN #在读取所有行内容前就开始执行，一般用来初始化操作

    ```
    eg：
    cat /etc/passwd | awk 'BEGIN {FS=":"} {print $1}'
    df -h |grep -v 'Filesystem' | awk '{printf $1} {printf "文件系统使用率："} {print $5}'
    df -h |grep -v 'Filesystem' | awk 'BEGIN {printf "文件系统使用情况：\n \n"} {printf $1}
    {printf "文 件系统使用率："} {print $5}'
    ```

  - END #结束的时候 执行

    ```
    df -h |grep -v 'Filesystem' | awk 'BEGIN {printf "文件系统使用情况：\n \n"} {printf $1}
    {printf "文件系统使用率："} {print $5} END {printf "一切正常 \n"}'
    ```

###### 处理海量数据之sed命令

- sed的应用场景：主要对数据进行处理（选取，新增，替换，删除，搜索）
  sed语法：

  - sed [选项] [动作] 文件名

- 常见的选项与参数：

  - -n #把匹配到的行输出打印到屏幕

  - p #以行为单位进行打印，通常与-n一起使用

    ```
    df -h | sed -n '2p'
    ```

  - d #删除

    ```
    df -h | sed '2d'
    ```

  - a #在行的下面插入新的内容

    ```
    df -h | sed '2a 1234567890'
    ```

  - i #在行的上面插入新的内容

    ```
    sed -n '/tmpfs/p' df.txt
    ```

  - c #替换 

    ```
    df -h | sed '2i 1234567890'
    ```

  - 指定字符串替换：s/要被取代的内容/新的字符串/g #指定内容进行替换

    ```
    df -h | sed '2c 1234567890'
    ```

  - -i #对源文件进行修改(高危操作，慎用，用之前需要备份源文件)

    ```
    df -h | sed 's/centos-root/Centos7/g'
    ```

  - 搜索：在文件中搜索内容

    ```
    sed -i 's/Centos7/Centos8/g' df.txt
    ```

  - -e #表示可以执行多条动作

    ```
    sed -e 's/Centos8/Centos7/g' -e 's/tmpfs/TMP/g' df.txt >123.tz
    ```

#### linux服务器常用企业服务的安装

#####  Linux下常用软件安装方式

###### rpm安装

- rpm安装优点：

  - 软件已经编译打包，所以传输和安装方便，让用户免除编译
  - 在安装之前，会先检查系统的磁盘、操作系统版本等，避免错误安装

- rpm安装缺点：

  - 软件包安装的环境必须与打包时的环境一致或相当
  - 必须安装了软件的依赖包

- RPM包的命名规则：

  ```
  which-2.20-7.el7.x86_64.rpm
  which #代表的是软件名称
  2.20 #代表的是软件版本号；
  7 #代表的是发布版本号，指的是这个rpm软件包是第几次编译生成的
  el7 #代表的是企业版的7操作系统
  X86 #代表的是CPU架构
  64 #代表的是系统的位数
  ```

- 安装rpm软件包：

  ```
  -i #install 安装软件包
  -v #输出更多的详情信息
  -h #输出哈希标记（#）
  --nodeps #不验证软件的依赖
  rpm -ivh zsh-5.0.2-7.el7.x86_64.rpm
  rpm -ivh mariadb-server-5.5.35-3.el7.x86_64.rpm --nodeps
  ```

- rpm包下载地址

  ```
  http://rpmfind.net/
  http://rpm.pbone.net/
  http://www.rpmseek.com/index.html
  ```

- rpm 查询功能：rpm -q

  - -a #查询所有已安装的软件包 rpm -qa zsh
  - -f #查询文件所属软件包 rpm -qf /usr/bin/zsh
  - -p #查询软件包
  - -i #显示软件包信息
  - -l #显示软件包中的文件列表
  - -d #显示被标注为文档的文件列表
  - -c #显示被标注为配置文件的文件列表

- rpm 包升级:

  - -U #升级rpm软件服务
    rpm -Uvh zsh-5.0.2-7.el7.x86_64.rpm

- rpm 包卸载:

  - -e #卸载
    rpm -e zsh

###### yum安装

​	yum安装：基于 C/S 架构，yum安装称之为傻瓜式安装
​	yum安装优点：方便快捷，不用考虑包依赖，自动下载软件包。
​	yum安装缺点：人为无法干预，无法设定想要的参数

- 配置本地yum源：
  - 配置文件的路径：/etc/yum.repos.d/

    ```
    [Centos7-yum] #yum源名称，唯一的，用来区分不同的 yum 源
    name=Centos7-source #对yum源描述信息
    baseurl=file:///mnt #yum源的路径（repodata目录所在的目录）
    enabled=1 #表示启用 yum 源
    gpgcheck=0 #为1表示使用公钥检验 rpm 的正确性
    ```

- yum安装方式的使用：

  - yum repolist #查看yum源列表
  - yum clean all #清空之前yum缓存
  - yum makecache #创建yum缓存，为后续安装更加快速
  - yum -y install #安装软件 yum -y install zsh
  - yum info zsh #查看zsh软件包信息（不管安装了没都会有信息）
  - yum info installed zsh #查看已经安装好的软件信息
  - yum -remove zsh #卸载软件
  - yum search gcc #搜索gcc软件
  - yum update #升级软件

###### 源码编译安装方式

​	源码安装优点：编译安装过程，可以设定参数，指定安装目录，按照需求进行安装，指定安装的版本，灵活性比较大。
​	源码安装的缺点：需要对依赖包一个一个的进行安装，不敢随便升级，一升级可能会由于依赖包的是不能使用导致一系列连锁反应

- 源码编译安装软件包4大步骤：

1. 解压源码包

   ```
   tar -xf 源码包
   ```

2. 配置

   ```
   进入解压后的目录，用./configure命令来配置相关信息（比如指定安装目录 --
   prefix=/usr/local/nginx）和生成Makefile文件
   ```

3. 编译

   ```
   make -j4
   ```

4. 安装

   ```
   make install
   ```

##### JDK8安装

1. 全局环境变量的配置文件：

   ```
   vi /etc/profile
   ```

   ```
   export JAVA_HOME=/usr/local/jdk1.8 #这个路径要改，其余不需要改
   export JRE_HOME=$JAVA_HOME/jre
   export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
   export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
   java version "1.8.0_211"
   ```

2. 加载环境变量：

   ```
   source /etc/profile
   ```

3. 检查是否安装成功：

   ```
   java -version
   ```

##### 部署tomcat网站服务器

1. 下载：

   ```
   yum install -y wget
   wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.43/bin/apachetomcat-8.5.43.tar.gz
   ```

2. 解压：

   ```
   tar -xf apache-tomcat-8.5.43.tar.gz
   mv apache-tomcat-8.5.43 /usr/local/tomcat8
   ```

3. tomcat重要目录介绍

   ```
   [root@localhost tomcat8]# ls -lrt /usr/local/tomcat8
   total 132
   drwxr-x---. 7 root root 76 Jul 5 04:53 webapps
   -rw-r-----. 1 root root 16262 Jul 5 04:56 RUNNING.txt
   -rw-r-----. 1 root root 7139 Jul 5 04:56 RELEASE-NOTES
   -rw-r-----. 1 root root 3255 Jul 5 04:56 README.md
   -rw-r-----. 1 root root 1726 Jul 5 04:56 NOTICE
   -rw-r-----. 1 root root 57011 Jul 5 04:56 LICENSE
   -rw-r-----. 1 root root 5407 Jul 5 04:56 CONTRIBUTING.md
   -rw-r-----. 1 root root 19534 Jul 5 04:56 BUILDING.txt
   drwxr-x---. 2 root root 4096 Aug 1 23:33 lib
   drwxr-x---. 2 root root 29 Aug 1 23:33 temp
   drwxr-x---. 2 root root 4096 Aug 1 23:33 bin
   drwx------. 3 root root 4096 Aug 1 23:43 conf
   drwxr-x---. 2 root root 4096 Aug 1 23:43 logs
   drwxr-x---. 3 root root 21 Aug 1 23:43 work
   bin：存放可执行命令,比如开启和关闭;
   conf：配置文件；
   Context.xml：Tomcat公用的环境配置，tomcat 服务器会定时去扫描这个文件
   web.xml：Web应用程序描述文件，都是关于是Web应用程序的配置文件
   server.xml：可以设置tomcat的端口号，添加虚拟机这些的，是对服务器的设置
   tomcat-users.xml：用户配置文件
   webapps：发布web应用；
   lib：库文件；
   ```

4. 关闭防火墙

   ```
   systemctl stop firewalld.service
   ```

5. 启动tomcat

   ```
   sh startup.sh
   ```

6. 测试能否访问测试页面

   ```
   IP地址:8080
   ```

##### 源码部署apache网站服务器//TODO

​	我已经嗝屁了........

##### 源码部署nginx网站服务器

1. 安装gcc编译环境：

   ```
   yum install -y gcc-c++
   ```

2. 安装zlib-devel库：

   ```
   yum install -y zlib-devel
   ```

3. 安装OpenSSL密码库：

   ```
   yum install -y openssl openssl-devel
   ```

4. 安装pcre正则表达式库：

   ```
   下载地址：https://ftp.pcre.org/pub/pcre/
   tar -xf pcre-8.43.tar.gz
   cd pcre-8.43
   mkdir -p /usr/local/pcre
   ./configure --prefix=/usr/local/pcre
   make && make install
   ```

5. 下载编译安装nginx：

   ```
   nginx下载官网：http://nginx.org/en/download.html
   wget http://nginx.org/download/nginx-1.16.0.tar.gz
   mkdir -p /usr/local/nginx
   tar -xf nginx-1.16.0.tar.gz
   cd nginx-1.16.0
   ./configure --prefix=/usr/local/nginx --with-http_ssl_module --withhttp_
   stub_status_module --with-pcre
   make && make install
   ```

6. 启停nginx服务：

   ```
   启动：
   /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
   测试：
   /usr/local/nginx/sbin/nginx -t
   关闭：
   /usr/local/nginx/sbin/nginx -s stop
   ```

##### 源码安装mysql5.7

1. 关闭selinux，关闭防火墙：

   ```
   systemctl stop firewalld.service
   vi /etc/selinux/config
   SELINUX=disabled
   ```

2. 安装cmake工具：

   ```
   yum -y install cmake
   ```

3. 下载boost路径（mysql5.7.17的必需依赖组件）：

   ```
   tar -xf boost_1_59_0.tar.gz
   ```

4. yum安装其它依赖组件：

   ```
   yum -y install gcc gcc-c++ bzip2 bzip2-devel bzip2-libs python-devel ncurses
   ncurses-devel openssl openssl-devel
   ```

5. 创建路径：

   ```
   mkdir -p /usr/local/mysql
   mkdir -p /data/mydata
   ```

6. 创建mysql用户：

   ```
   useradd -M -s /sbin/nologin mysql
   ```

7. 使用cmake工具对mysql5.7.17进行环境收集检验与配置相关模块：

   ```
   解压mysql源码包，并进入解压后的路径
   tar -xf mysql-5.7.17.tar.gz
   cd mysql-5.7.17
   cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \-DMYSQL_DATADIR=/data/mydata \-
   DSYSCONFDIR=/etc \-DWITH_INNOBASE_STORAGE_ENGINE=1 \-DWITH_ARCHIVE_STORAGE_ENGINE=1 \-
   DWITH_BLACKHOLE_STORAGE_ENGINE=1 \-DWITH_READLINE=1 \-DMYSQL_UNIX_ADDR=/tmp/mysql.sock
   \-DWITH_SSL=system \-DWITH_ZLIB=system \-DDEFAULT_CHARSET=utf8 \-
   DDEFAULT_COLLATION=utf8_general_ci \-DDOWNLOAD_BOOST=1 \-DWITH_BOOST=../boost_1_59_0
   \-DENABLE_DOWNLOADS=1
   参数详细信息解释：
   -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \ #指定mysql数据库安装目录
   -DMYSQL_DATADIR=/data/mydata \ #指定数据库文件路径
   -DSYSCONFDIR=/etc \ #指定配置文件目录
   -DWITH_INNOBASE_STORAGE_ENGINE=1 \ #安装INNOBASE存储引擎
   -DWITH_ARCHIVE_STORAGE_ENGINE=1 \ #安装ARCHIVE存储引擎
   -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \ #安装BLACKHOLE存储引擎
   -DWITH_READLINE=1 \ #使用readline功能
   -DMYSQL_UNIX_ADDR=/tmp/mysql.sock \ #连接文件位置
   -DWITH_SSL=system \ #表示使用系统上的自带的SSL库
   -DWITH_ZLIB=system \ #表示使用系统上的自带的ZLIB库
   -DDEFAULT_CHARSET=utf8 \ #指定默认使用的字符集编码
   -DDEFAULT_COLLATION=utf8_general_ci \ #指定默认使用的字符集校对规则
   -DDOWNLOAD_BOOST=1 \
   -DWITH_BOOST=../boost_1_59_0 \ #指定Boost库的位置，mysql5.7必须添加该参数
   -DENABLE_DOWNLOADS=1 #支持下载可选文件
   ```

8. 编译并安装：

   ```
   make -j 4 && make install
   ```

9. 初始化mysql：

   ```
   /usr/local/mysql/bin/mysqld \--initialize \--user=mysql \--basedir=/usr/local/mysql \-
   -datadir=/data/mydata \--socket=/tmp/mysql.sock
   ```

10. 对mysql的相关路径进行更改权限

    ```
    chown -R mysql:mysql /usr/local/mysql /data/mydata
    ```

11. 修改配置文件：

    ```
    vi /etc/my.cnf
    [mysqld]
    datadir=/data/mydata
    socket=/tmp/mysql.sock
    symbolic-links=0
    [mysqld_safe]
    log-error=/usr/local/mysql/log/mysql.errlog
    pid-file=/data/mydata/$hostname.pid
    ```

12. 启停mysql：

    ```
    [root@localhost support-files]# ./mysql.server start
    Starting MySQL.2019-08-03T14:19:37.028727Z mysqld_safe error: log-error set to
    '/usr/local/mysql/log/mysql.errlog', however file don't exists. Create writable for
    user 'mysql'.
    ERROR! The server quit without updating PID file
    (/data/mydata/localhost.localdomain.pid).
    解决：
    touch /usr/local/mysql/log/mysql.errlog
    chown -R mysql:mysql /usr/local/mysql/log/mysql.errlog
    启动：
    /usr/local/mysql/support-files/mysql.server start
    关闭：
    /usr/local/mysql/support-files/mysql.server stop
    ```

13. 登录mysql：

    ```
    /usr/local/mysql/bin/mysql -uroot -p
    ```

14. 修改mysql密码：

    ```
    set password for 'root'@'localhost'=password('密码');
    mysql> flush privileges;
    ERROR 1146 (42S02): Table 'mysql.servers' doesn't exist
    use mysql;
    drop table if exists mysql.servers;
    CREATE TABLE `servers` (
    `Server_name` char(64) NOT NULL,
    `Host` char(64) NOT NULL,`Db` char(64) NOT NULL,
    `Username` char(64) NOT NULL,
    `Password` char(64) NOT NULL,
    `Port` int(4) DEFAULT NULL,
    `Socket` char(64) DEFAULT NULL,
    `Wrapper` char(64) NOT NULL,
    `Owner` char(64) NOT NULL,
    PRIMARY KEY (`Server_name`)
    ) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='MySQL Foreign Servers table';
    ```

15. 添加MySQL服务并设置mysql开机启动：

    ```
    cp -a /usr/local/mysql/support-files/mysql.server /etc/rc.d/init.d/mysql
    chkconfig --add mysql
    chkconfig --list mysql
    mysql 0:off 1:off 2:on 3:on 4:on 5:on 6:off
    chkconfig命令主要用来更新（启动或停止）和查询系统服务的运行级信息
    等级0表示：表示关机
    等级1表示：单用户模式
    等级2表示：无网络连接的多用户命令行模式
    等级3表示：有网络连接的多用户命令行模式
    等级4表示：不可用
    等级5表示：带图形界面的多用户模式
    等级6表示：重新启动
    使用mysql服务的方式操作启停mysql服务：
    service mysql start #启动mysql服务器
    service mysql stop #关闭mysql服务器
    service mysql restart #重启mysql服务器
    ```

16. 设置mysql环境变量

    ```
    ln -s /usr/local/mysql/bin/* /usr/sbin/
    ```

##### 源码部署php服务与nginx 的整合

1. 源码编译安装：

   ```
   安装依赖组件：
   yum -y install gcc gcc-c++ bzip2 bzip2-devel bzip2-libs python-devel ncurses ncursesdevel
   openssl openssl-devel
   yum install -y libxml2-devel
   解压php并进入解压后php包：tar -xf php-5.5.35.tar.gz && cd php-5.5.35
   ./configure --prefix=/usr/local/php/ --enable-fpm --with-configfile=/
   usr/local/php/etc
   编译安装：
   make -j 4 && make install
   ```

2. 修改配置文件：

   ```
   cp -a php.ini-production /usr/local/php/etc/php.ini
   cp -a /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
   ```

3. 启停php服务器：

   ```
   /usr/local/php/sbin/php-fpm #启动
   php的默认端口是：9000
   cd /usr/local/php/sbin && pkill php-fpm
   ```

4. 启停php服务器

   ```
   /usr/local/php/sbin/php-fpm #启动
   php的默认端口是：9000
   cd /usr/local/php/sbin && pkill php-fpm #关闭
   ```

5. 整合nginx测试php

   ```
   修改nginx配置文件并添加以下内容：vi /usr/local/nginx/conf/nginx.conf
   location ~ \.php$ {
   root /usr/local/nginx/html;
   fastcgi_pass 127.0.0.1:9000;
   fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
   include fastcgi_params;
   }
   修改后对nginx进行重启：/usr/local/nginx/sbin/nginx -s reload
   ```

   ```
   FastCGI是 一个 在HTTP服务器和动态脚本语言间通信的接口
   fastcgi_pass 127.0.0.1:9000; #设置监听端口
   fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;#设置脚本文件请求的路径
   include fastcgi_params; #引入fastcgi的配置文件
   ```

6. 在nginx的网站发布路径下创建index.php文件

   ```
   vi /usr/local/nginx/html/index.php
   <?php
   phpinfo();
   ?>
   ```

#### shell脚本编程

- shell的优点：
  1. 易用 #直接在linux系统上使用，不需要编译
  2. 高效 #程序开发的效率非常高，依赖于功能强大的命令可以迅速地完成开发任务
  3. 简单 #语法和结构比较简单，易于掌握
- shell应用场景：
  1. 监控linux系统的健康度
  2. 数据的处理 #日志的切割、分析、统计等
  3. 与数据库交互 #对数据库进行增，删，改，查等操作
  4. 监控进程，自动化启停服务
  5. 完成一些重复性的工作

##### 企业实战之shell脚本与crontab定时器的运用

- crond服务：
  以守护进程方式在无需人工干预的情况下来处理着一系列作业和指令的服务

- crond服务的启停命令：

  ```
  启动
  systemctl start crond.service
  查看状态：
  systemctl status crond.service
  停止
  systemctl stop crond.service
  重新启动
  systemctl restart crond.service
  ```

- crontab定时器的使用

  ```
  crontab -l #列出crontab有哪些任务
  crontab -e #编辑crontab任务
  crontab -r #删除crontab里的所有任务
  ```

- crontab的例子
  略...

- 实战nginx服务器日志定时切割

  ```
  #!/bin/bash
  #Auto cut nginx log script.
  #nginx日志路径
  logs_path=/usr/local/nginx/logs
  YesterDay=$(date -d 'yesterday' +%Y-%m-%d)
  #移动日志并以日期改名
  mv ${logs_path}/access.log ${logs_path}/access_${YesterDay}.log
  mv ${logs_path}/error.log ${logs_path}/error_${YesterDay}.log
  #向nginx主进程发送信号，重新生成日志文件
  kill -USR1 $(cat /usr/local/nginx/logs/nginx.pid)
  ```

#### 企业实战篇

##### 静态ip地址配置

- 网卡的路径：

  ```
  /etc/sysconfig/network-scripts/ifcfg-enoxxxxxxxx
  ```

- alias命令实现别名：

  ```
  临时设置别名：
  alias vinet='vi /etc/sysconfig/network-scripts/ifcfg-eno16777728'
  查看别名：
  alias
  取消临时别名：
  alias vinet
  永久设置别名：vi /root/.bashrc
  alias vinet='vi /etc/sysconfig/network-scripts/ifcfg-eno16777728'
  加载使立即生效：
  source /root/.bashrc
  ```

- 修改ip地址为静态

  ```
  BOOTPROTO="static"
  IPADDR=xxx.xxx.xxx.xxx
  GATEWAY=xxx.xxx.xxx.xxx
  NETMASK=255.255.255.0
  ONBOOT="yes"
  修改以上信息，以下是我网卡信息
  HWADDR="00:0c:29:dc:47:58"
  TYPE="Ethernet"
  BOOTPROTO="static"
  DEFROUTE="yes"
  PEERDNS="yes"
  PEERROUTES="yes"
  IPV4_FAILURE_FATAL="no"
  IPADDR=192.168.10.100
  GATEWAY=192.168.10.2
  NETMASK=255.255.255.0
  IPV6INIT="yes"
  IPV6_AUTOCONF="yes
  IPV6_DEFROUTE="yes"
  IPV6_PEERDNS="yes"
  IPV6_PEERROUTES="yes"
  IPV6_FAILURE_FATAL="no"
  NAME="eno16777728"
  UUID="3199add9-379c-43a9-bab1-ae4e05c0f2cc"
  ONBOOT="yes"
  ```

- 重启网卡

  ```
  systemctl restart network.service
  ```

- ping不通域名：

  ```
  [root@localhost ~]# ping www.baidu.com
  ping: unknown host www.baidu.com
  解决：vi /etc/resolv.conf 加上以下域名服务器解析地址
  nameserver 114.114.114.114
  nameserver 8.8.8.8
  nameserver 1.1.1.1
  ```

##### 实战修改linux系统主机名

- 查看主机名命令：

  ```
  hostname
  ```

- 修改主机名的命令：

  ```
  hostnamectl set-hostname NEW_NAME
  ```

- 修改后记得重启使得生效：

  ```
  reboot
  ```

- 修改/etc/hosts文件：

  ```
  vi /etc/hosts
  ```

##### 实战ssh免密远程登录其它机器

1. 执行命令创建密钥：ssh-keygen -t rsa

2. 拷贝文件到目标服务器并重命名为authorized_keys

   ```
   #eg:
   scp /root/.ssh/id_rsa.pub root@xdapp2:/root/.ssh/authorized_keys
   ```

##### 实战搭建nfs文件共享服务器//TODO

已经脑死亡了....

