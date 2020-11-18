---
title: Jenkins持续集成-Git-Gitlab-Sonar__backup
date: 2020-11-05 08:55:08
tags: git gitlab jekenis
category: 内在修行
---

### Git

#### git基本命令

##### git入门命令

- git --help 

  调出Git的帮助文档 

- git +命令 --help

  查看某个具体命令的帮助文档 

- git --version

   查看git的版本 

- git init 

  生成空的本地仓库 

- git add 

  将文件添加到暂存区

- git remote

   用于管理远程仓库

- git push -u origin master

   往名字为origin的仓库的master分支上提交变更

- git fetch 

  拉取远程仓库的变更到本地仓库

- git merge origin/master 

  将远程的变更，合并到本地仓库的master分支

- git pull

  不建议使用 等同于fetch之后merge

##### git文件状态

- git status

  用于查看git的状态

- git rm

  用于git文件的删除操作 如果只是 git rm --cache 仅删除暂存区里的文件 如果不加--cache 会删除工作区里的文件 并提交到暂存区

- git checkout

  直接加文件名 从暂存区将文件恢复到工作区，如果工作区已经有该文件，则会选择覆盖加了【分支名】 +文件名 则表示从分支名为所写的分支名中拉取文件 并覆盖工作区里的文件

  ```
  新建文件--->Untracked 
  使用add命令将新建的文件加入到暂存区--->Staged 
  使用commit命令将暂存区的文件提交到本地仓库--->Unmodified 
  如果对Unmodified状态的文件进行修改---> modified 
  如果对Unmodified状态的文件进行remove操作--->Untracked
  ```

##### git分支

- git branch +分支名  

  切换分支 。git branch 不加任何参数，列出所有的分支，分支前面有*号，代表该分支为当前所在分支

-  git branch -d 分支名

  创建分支的时候，分支名不用使用特殊符号 ，不能删除当前所在的分支 

- git branch -m 旧分支名 新分支名  

   改名

- git checkout 分支名 

  切换分支，如果在分支上面对文件进行修改之后，没有commit就切换到另外一个分支b， 这个时候会报错，因为没有commit的文件在切换分支之后会不覆盖。所以Git 报错提示。

- git checkout -f 分支名 

  强制切换到分支，如果当前有为提交的变更，会直接丢弃 -f 参数一定一定要非常非常小心使用，一般情况下不建议使用，除非真的要强制去执行

##### git log

- log命令的作用

  用于查看git的提交历史

- git log命令显示的信息的具体含义

  commit 4a70ceb24b6849ad830d6af5126c9227b333d2d1 --SHA-1 校验和 commit id Author: wiggin zyaire0001@163.com --作者跟邮箱概要信息 Date: Fri Nov 6 11:51:02 2020 +0800 --提交时间

  v2 --commit的时候，使用-m选项说写一段概要说明 日常在使用commit的时候，-m选项所写得内容一定不能随便写 “修改了登陆的bug”--》“新增用户管理中心”

- git log -数字 

  表示查看最近几次的提交

- git log -p -2 

  显示最近两次提交的不同点

- git log --author 

  查看具体某个作者的提交

- git log --oneline 

  输出简要的信息

- git log --graph 

  以一个简单的线串联起整个提交历史

- git log 

  输出信息的定制

##### git diff

##### 分支合并及冲突解决

##### git标签

##### gitignore

### Gitlab教程

#### 安装

1. 在防火墙里开放http跟ssh端口

   ```
   yum install lokkit -y				
   #GitLab使用postfix发送邮件 
   yum install curl openssh-server openssh-clients postfix cronie -y
   #启动
   systemctl start postfix.service
   #设置开机自启动
   systemctl enable postfix.service
   ```

2. 添加gitlab仓库，并安装

   ```
   #下载rpm包(下载地址：https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/)
   wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.2.7-ce.0.el7.x86_64.rpm 
   #安装
   rpm -i gitlab-ce-10.2.7-ce.0.el7.x86_64.rpm 
   #如果报错 policycoreutils-python is needed by gitlab-ce-10.2.7-ce.0.el7.x86_64
   yum install policycoreutils-python -y
   ```

#### 配置gitlab

1. 基本配置

   ```
   gitlab-ctl reconfigure
   vim /etc/gitlab/gitlab.rb
   #修改external_url为gitlab机子的ip+要使用的端口
   	如：http://192.168.56.101:8888
   #修改nginx['listen_port'] = 8888
   #重启
   gitlab-ctl reconfigure
   gitlab-ctl restart
   #可能配置防火墙
   ```

2. 配置邮件服务

   - 开启QQ邮箱的smtp服务(不建议使用163邮箱，发几次之后，就不能发送)

     设置--》账户--》smtp--》密保验证--》验证成功返回一串字符串，形状如（ausdixersybgcgid）

   - 修改gitlab配置

     ```
     vim /etc/gitlab/gitlab.rb
     #放开注释
     gitlab_rails['smtp_enable'] = true
     gitlab_rails['smtp_address'] = "smtp.qq.com" #照着改
     gitlab_rails['smtp_port'] = 465  #这里默认就行
     gitlab_rails['smtp_user_name'] = "1234@qq.com"#填写自己的qq邮箱
     gitlab_rails['smtp_password'] = "ausdixersybgcgid"#开通smtp返回的字符
     gitlab_rails['smtp_domain'] = "qq.com"
     gitlab_rails['smtp_authentication'] = "login"
     gitlab_rails['smtp_enable_starttls_auto'] = true
     gitlab_rails['smtp_tls'] = true
     
     user['git_user_email'] = "1403780990@qq.com"
     gitlab_rails['gitlab_email_from'] = '1403780990@qq.com'
     按esc退出到命令行模式
     之后:wq 保存并退出
     gitlab-ctl reconfigure
     ```

   - 测试邮件服务是否正常

     ```
     gitlab-rails console
     Notify.test_email('接收方邮件地址','邮件标题','邮件内容').deliver_now
     ```

     按回车，测试发送。

#### gitlab分支及标签保护

#### gitlab常用命令

```
gitlab常用命令：

gitlab-ctl start    # 启动所有 gitlab 组件；

gitlab-ctl stop        # 停止所有 gitlab 组件；

gitlab-ctl restart        # 重启所有 gitlab 组件；

gitlab-ctl status        # 查看服务状态；

vim /etc/gitlab/gitlab.rb        # 修改gitlab配置文件；

gitlab-ctl reconfigure        # 重新编译gitlab的配置；

gitlab-rake gitlab:check SANITIZE=true --trace    # 检查gitlab；

gitlab-ctl tail        # 查看日志；

gitlab-ctl tail nginx/gitlab_access.log

cat /opt/gitlab/embedded/service/gitlab-rails/VERSION  #查看gitlab版本
```

#### gitlab的备份与恢复

1. 备份

   ```
    # 可以将此命令写入crontab，以实现定时备份
   /usr/bin/gitlab-rake gitlab:backup:create
   ```

2. 恢复

   ```
   # 停止unicorn和sidekiq，保证数据库没有新的连接，不会有写数据情况
   gitlab-ctl stop unicorn
   gitlab-ctl stop sidekiq
   # 进入备份目录进行恢复，1550640732_2019_02_20_11.7.5为备份文件的数字部分
   cd /var/opt/gitlab/backups
   gitlab-rake gitlab:backup:restore BACKUP=1550640732_2019_02_20_11.7.5
   cd -
   # 启动unicorn和sidekiq
   gitlab-ctl start unicorn
   gitlab-ctl start sidekiq
   ```

### 敏捷持续集成

- 什么是持续集成？

  持续集成是一种软件开发实践，即团队开发成员经常集成他们的工作，通过每个成员每天至少集成一次，也就意味着每天可能会发生多次集成。每次集成都通过自动化的构建（包括编译，发布，自动化测试）来验证，从而尽早地发现集成错误。

- 好处

  节省人力成本 加快软件开发进度 实时交付

#### 安装JDK、Maven环境

```
 vim /etc/profile
 #jdk环境变量
            JAVA_HOME=/usr/local/jdk1.8.0_91
            export JAVA_HOME
            CLASSPATH=.:$JAVA_HOME/lib
            export CLASSPATH
            PATH=$PATH:$JAVA_HOME/bin:$CLASSPATH
            export PATH            
 #maven环境变量
         vim /etc/profile
         在最下面，按i进入insert模式，添加一下内容
            MAVEN_HOME=/usr/local/apache-maven-3.5.3
            export MAVEN_HOME
            PATH=$PATH:$MAVEN_HOME/bin
            export PATH
 #使生效
            source /etc/profile
 #java -version 查看jdk是否配置成功
 #mvn --help查看Maven是否配置成功
```

#### Maven私服之Nexus安装

##### 安装

- 官网

  ```
https://www.sonatype.com/download-oss-sonatype
  ```

1. 解压

2. 修改配置文件

   ```
   # 修改对应的端口
   vim /usr/local/nexus-3.12.1-01/etc/nexus-default.properties
   # 修改防火墙
   vim /etc/sysconfig/iptables
   ```

3. 准备

   ```
   #新增nexus用户
   useradd nexus
   #赋权(需要root用户)
   chown -R nexus:nexus 安装路径
   chown -R nexus:nexus sonatype-work/
   #切换用户
   su nexus
   #启动
   cd bin/./nexus start
   #./nexus run可以查看启动日志
   ```

4. 登陆

   - 登陆

   ```
   http://192.168.56.102:8081/ #自己配置的端口
   #账号 admin
   #密码 admin123
   ```

   - 修改ulimt

   ```
   vim /etc/security/limits.conf
   #新增(需要root用户)
   * soft nofile 65535
   # * 表示全部用户
   * hard nofile 65535
   ```

   - 配置开机自启动

   ```
   vim /etc/rc.d/rc.local
   #添加
   su - nexus -c '/usr/local/nexus-3.12.1-01/bin/nexus start'
   ```

##### 使用

- 配置代理

  选择阿里云http://maven.aliyun.com/nexus/content/groups/public/

- 本地maven配置

  修改maven目录下的conf/setting.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <pluginGroups/>
  <proxies/>
  <servers>
    <server>
      <id>xdclass-releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>xdclass-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
  </servers>
  <mirrors/>
  <profiles>
    <profile>
      <id>xdclass</id>
      <activation>
        <activeByDefault>false</activeByDefault>
      </activation>
      <!-- 私有库地址-->
      <repositories>
        <repository>
          <id>xdclass</id>
          <url>http://192.168.56.101:8081/repository/maven-public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
      </repositories>
      <!--插件库地址-->
      <pluginRepositories>
        <pluginRepository>
          <id>xdclass</id>
          <url>http://192.168.56.101:8081/repository/maven-public/</url>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
  <activeProfiles>
    <activeProfile>xdclass</activeProfile>
  </activeProfiles>
</settings>
```

- 修改编辑器中maven的配置，将配置指向setting.xml
- 修改pom

```
<!--pom.xml 远程仓库的配置  id要跟本地maven的setting.xml相同 -->
  <distributionManagement>
        <repository>
            <id>xdclass-releases</id>
            <name>Ruizhi Release Repository</name>
            <url>http://192.168.56.101:8081/repository/maven-releases/</url>
        </repository>

        <snapshotRepository>
            <id>xdclass-snapshots</id>
            <name>Ruizhi Snapshot Repository</name>
            <url>http://192.168.56.101:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```

- 测试是否nexus搭建成功

  pom添加本地没有的依赖，看nexus会不会代理 mvn deploy 看是否成功推送至nexus

编译安装mysql

- 安装依赖

  ```
  yum install make cmake gcc gcc-c++ bison bison-devel ncurses ncurses-devel autoconf
  #下载boost
  mkdir /usr/local/software/boost
  wget https://sourceforge.net/projects/boost/files/latest/download --no-check-certificate
  ```

- 添加用户并创建相应目录存放数据

  ```
  useradd mysql
  #改变权限
  mkdir data logs temp
  chown -R mysql:mysql data logs temp
  ```

- 执行cmake

  ```
      cmake \
          -DCMAKE_INSTALL_PREFIX=/usr/local/software/mysql-5.7.17 \
          -DMYSQL_UNIX_ADDR=/usr/local/mysql-5.7.17/software/mysql.sock \
          -DDEFAULT_CHARSET=utf8 \
          -DDEFAULT_COLLATION=utf8_general_ci \
          -DWITH_MYISAM_STORAGE_ENGINE=1 \
          -DWITH_INNOBASE_STORAGE_ENGINE=1 \
          -DWITH_ARCHIVE_STORAGE_ENGINE=1 \
          -DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
          -DWITH_MEMORY_STORAGE_ENGINE=1 \
          -DWITH_READLINE=1 \
          -DENABLED_LOCAL_INFILE=1 \
          -DMYSQL_DATADIR=/home/mysql/data \
          -DMYSQL_USER=mysql \
          -DMYSQL_TCP_PORT=3306 \
          -DWITH_BOOST=/usr/local/software/boost_1_59_0
  #如果出现
  #CMake Error: The source directory "/usr/local/software/boost_1_59_0" does not appear to contain CMakeLists.txt.
  #可能是mysql版本有问题，下载source code version
  #或者重新安装cmake尝试一下
  ```

- 编译安装

  ```
  make && make install
  ```

- 修改mysql安装目录权限

  ```
  chown -R mysql:mysql /usr/local/software/mysql-5.7.17
  ```

- 初始化mysql

  ```
      mysqld --initialize --user=mysql --basedir=/usr/local/software/mysql-5.7.17 --datadir=/home/mysql/data
  ```

  产生密码  WfUqwfkdt0>g

- 修改环境变量

  ```
  vim /etc/profile
  #最后一行添加
  PATH=usr/local/software/mysql-5.7.17/lib:$PATH
  export PATH
  #使生效
  source /etc/profile
  ```

- 删除/etc下的my.cnf

  ```
  rm /etc/my.cnf
  #因为会影响mysql的启动
  ```

- 复制服务启动脚本

  ```
  cp usr/local/software/mysql-5.7.17/support-files/mysql.server /etc/init.d/mysql
  ```

- 启动 MySQL 服务

  ```
  service mysql start
  ```

- 设置mysql服务开机自启动

  ```
  systemctl enable mysqld.service
  ```

  

- 登陆mysql并设置可远程登陆

  ```
  mysql -uroot -p
  #修改密码
  #先执行 mysqld_safe --skip-grant-tables &   ---（设置成安全模式）&，表示在后台运行   会实现免密登录
  SET PASSWORD = PASSWORD('1998aaaa')
  ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
  flush privileges;
  #退出
  exit
  #重新登录
  #允许远程登录
  GRANT ALL PRIVILEGES ON . TO 'root'@'%' IDENTIFIED BY '1998aaaa' WITH GRANT OPTION;
  flush;
  #退出
  
  
  #开启防火墙
  vim /etc/sysconfig/iptables
  #加入
  -A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
  
  #或者
  firewall-cmd --permanent --add-port=3306/tcp
  firewall-cmd --reload
  ```

#### sonarqube安装

- 解压安装

  ```
  unzip sonarqube-6.7.4.zip
  ```

- mysql里新增数据库

  ```
  CREATE DATABASE sonar DEFAULT CHARACTER SET utf8;
  ```

- 修改sonarqube相应的配置

  ```
  #修改conf目录下的配置文件
  vim /conf/sonar.properties
  #放开注释并修改
  sonar.jdbc.username=root
  sonar.jdbc.password=#密码
  sonar.jdbc.url=#改成自己创建的库名
  sonar.web.context=/sonar
  sonar.web.host=0.0.0.0
  ```

- 新增用户，并将目录所属权赋予该用户

  ```
  useradd sonar chown -R sonar:sonar sonarqube-6.7.4/
  ```

- 切换用户启动

  ```
  su sonar
  #在相应目录下
  ./sonar.sh start
  ```

- 开启防火墙访问

  ```
  http://192.168.56.101:9000/sonar
  ```

- Maven项目以下方式提交

  ```
  mvn sonar:sonar \ -Dsonar.host.url=http://192.168.56.101:9000/sonar \ -Dsonar.login=830edadfcb2c6326b1c6e2110f43c9f74d008450#这是生成的token
  ```

- Jenkins安装
  
  - 安装Tomcat

#### Jenkins安装

- 安装Tomcat

  ```
  useradd tomcat #新增一个名为tomcat的用户
  passwd tomcat #给tomcat用户设置密码
  chown -R tomcat:tomcat /usr/local/software/apache-tomcat-9.0.39 #将整个目录的所属权转移给tomcat用户、tomcat组
  ```

- 安装Jenkins

  - 将Jenkins上传到tomcat的webapp目录
-  vim conf/server.xml
  - 访问 http://192.168.56.101:9999/jenkins/pluginManager/advanced 拉到最底下将https改成http
  - 重启tomcat 浏览器打开http://192.168.56.101:9999/jenkins 
  - more /home/tomcat/.jenkins/secrets/initialAdminPassword 
- 选择默认安装

- 系统管理--->插件管理
  - 安装Maven Integration plugin --整合Maven
  - 安装SonarQube Scanner for Jenkins --整合Jenkins
  - Publish Over SSH --发布到远程服务器

- 配置

  - 系统配置

    - 配置jdk 

    - 配置maven 

    - 配置sonar 

    - 邮件配置 

      系统管理-->系统设置-->邮件通知-

      ```
       smtp服务器 smtp.qq.com 
       用户默认邮件后缀 @qq.com 
       勾选ssl 
       Reply-To Address发件者邮箱 
       之后测试一下配置，无误即可
      ```

  - 配置gitlab授权

    Credentials-->system-->Global credentials

  - 配置免密登陆

    - yum -y install openssh-clients 
    - ssh-keygen -t rsa -- 产生私钥 
    - 配置git登陆 
    - 将Jenkins所在机子的公钥 more ~/.ssh/id_rsa.pub 的内容拷贝到gitlab项目上

### 持续集成

#### 手动构建

#### 自动构建

- 分布式任务自动构建

