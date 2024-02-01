---
title: CentOS 8 安装 Mysql 5.7
date: 2021-06-17 17:14:46
tags: 
  - Linux
  - Mysql
categories: 
  - [Mysql]
  - [Linux]
comments: true
cover: img/index_img.png
---

在Centos8上用原来Centos7上安装MySQL5.7的方法会安装失败，显示mysql-community-server安装错误。我们用新的方法在Centos8上安装MySQL5.7

# 安装MySQL

## 添加MySQL存储库

禁用MySQL默认的AppStream存储库：

```shell
sudo dnf remove @mysql
sudo dnf module reset mysql && sudo dnf module disable mysql
```

centos8没有MySQL存储库，因此我们将使用centos 7存储库。创建一个新的存储库文件。

```shell
sudo vim /etc/yum.repos.d/mysql-community.repo
```

将以下数据插入上面的存储库中

```javascript
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=http://repo.mysql.com/yum/mysql-connectors-community/el/7/$basearch/
enabled=1
gpgcheck=0

[mysql-tools-community]
name=MySQL Tools Community
baseurl=http://repo.mysql.com/yum/mysql-tools-community/el/7/$basearch/
enabled=1
gpgcheck=0
```

## 安装MySQL(这里我选择MySQL5.7)

```shell
sudo dnf --enablerepo=mysql57-community install mysql-community-server
```

如果安装失败,使用以下方法进行安装

先下载rpm包

```shell
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-common-5.7.27-1.el6.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-libs-5.7.27-1.el6.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-client-5.7.27-1.el6.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-community-server-5.7.27-1.el6.x86_64.rpm
```

再安装

```shell
yum install -y mysql-community-common-5.7.27-1.el6.x86_64.rpm
yum install -y mysql-community-libs-5.7.27-1.el6.x86_64.rpm
yum install -y mysql-community-client-5.7.27-1.el6.x86_64.rpm
yum install -y mysql-community-server-5.7.27-1.el6.x86_64.rpm
```

## 下载完成后检查版本

```shell
[root@node1 ~]# rpm -qi mysql-community-server
Name        : mysql-community-server
Version     : 5.7.34
Release     : 1.el7
Architecture: x86_64
Install Date: Thu 27 May 2021 02:23:15 PM CST
Group       : Applications/Databases
Size        : 797908158
License     : Copyright (c) 2000, 2021, Oracle and/or its affiliates. All rights reserved. Under GPLv2 license as shown in the Description field.
Signature   : DSA/SHA1, Sat 27 Mar 2021 12:49:59 AM CST, Key ID 8c718d3b5072e1f5
Source RPM  : mysql-community-5.7.34-1.el7.src.rpm
Build Date  : Fri 26 Mar 2021 03:24:41 PM CST
Build Host  : pb2-el7-07.appad3iad.mysql2iad.oraclevcn.com
Relocations : (not relocatable)
Packager    : MySQL Release Engineering <mysql-build@oss.oracle.com>
Vendor      : Oracle and/or its affiliates
URL         : http://www.mysql.com/
Summary     : A very fast and reliable SQL database server
Description :
The MySQL(TM) software delivers a very fast, multi-threaded, multi-user,
and robust SQL (Structured Query Language) database server. MySQL Server
is intended for mission-critical, heavy-load production systems as well
as for embedding into mass-deployed software. MySQL is a trademark of
Oracle and/or its affiliates

The MySQL software has Dual Licensing, which means you can use the MySQL
software free of charge under the GNU General Public License
(http://www.gnu.org/licenses/). You can also purchase commercial MySQL
licenses from Oracle and/or its affiliates if you do not wish to be bound by the terms of
the GPL. See the chapter "Licensing and Support" in the manual for
further info.

The MySQL web site (http://www.mysql.com/) provides the latest news and
information about the MySQL software.  Also please see the documentation
and the manual for more information.

This package includes the MySQL server binary as well as related utilities
to run and administer a MySQL server.
```

出现以上信息说明安装成功

## 检查 mysql 源是否安装成功

```shell
yum repolist enabled | grep "mysql.*-community.*"
```

出现以下信息说明安装成功：

```shell
mysql-connectors-community MySQL Connectors Community                       141
mysql-tools-community      MySQL Tools Community                            105
mysql57-community          MySQL 5.7 Community Server
```

如果遇到版本不对，现象是客户端版本比较低，则需要执行更新操作：

```shell
dnf update mysql
```

## 启动MySQL

```shell
systemctl start mysqld
```

## 查看启动状态

```shell
systemctl status mysqld
```

出现以下信息，则启动成功

```shell
[root@node1 ~]# systemctl status mysqld
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) (thawing) since Thu 2021-05-27 14:35:11 CST; 4min 6s ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
  Process: 13463 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 13445 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 13466 (mysqld)
    Tasks: 29 (limit: 4752)
   Memory: 218.1M
   CGroup: /system.slice/mysqld.service
           └─13466 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

May 27 14:35:10 node1 systemd[1]: mysqld.service: Succeeded.
May 27 14:35:10 node1 systemd[1]: Stopped MySQL Server.
May 27 14:35:10 node1 systemd[1]: Starting MySQL Server...
May 27 14:35:11 node1 systemd[1]: Started MySQL Server.
```

## 设置开机启动

```shell
systemctl enable mysqld
```

## 刷新所有修改过的配置文件

```shell
systemctl daemon-reload
```

## 获取安装mysql后生成的临时密码，用于登录

```shell
grep 'temporary password' /var/log/mysqld.log
```

\# 如果出现如下列信息，密码为: **Ldse65Csow#g**

```shell
2021-05-27T06:25:51.106817Z 1 [Note] A temporary password is generated for root@localhost: Ldse65Csow#g
```

## 登录MySQL

```shell
mysql -uroot -p Ldse65Csow#g
```

\# 再输入上面查找得到的临时密码即可进入mysql

## 修改登录密码

```mysql
set global validate_password_policy=0;
set global validate_password_length=1;
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

### 添加远程登录用户(即本机访问服务器上的MySQL)

```mysql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'zhangsan（用户名）'@'%' IDENTIFIED BY 'Zhangsan2018!（密码）' WITH GRANT OPTION;
# 或者直接将root权限修改为可以通过远程访问(但不推荐)
mysql> use mysql;
mysql> UPDATE user SET Host='%' WHERE User='root';
mysql> flush privileges;
```

## 设置默认编码为utf-8（mysql安装后默认不支持中文）

```shell
vim /etc/my.cnf
# 进入文件后添加下面的配置即可
[mysqld]
character-set-server=utf8
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
```

## 重启MySQL服务并进入MySQL

```shell
shell> systemctl restart mysqld
shell> mysql -uroot -p
mysql> show variables like 'character%';
```

出现如下则说明编码修改完成

```mysql
mysql> show variables like 'character%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```

## 退出MySQL

```mysql
mysql> quit
```

使用本机电脑navicat/sqlyog等一系列客户端工具连接服务器上的mysql，用户名和密码为远程用户的用户名和密码，如果是将root权限修改为可以远程访问，就用root访问。