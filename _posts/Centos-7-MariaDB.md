title: Centos 7 MariaDB
date: 2016-01-21 16:26:53
tags: "linux"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
由于CentOS 7已经将MySQL替换为MariaDB了，当你`yum install mysql-server`的时候会提示没有这个包，
换用`yum install mariadb mariadb -y` 安装完毕，
使用命令`service mysql start`会出现错误：

```shell
[root@localhost bin]# service mysql start
Redirecting to /bin/systemctl start  mysql.service
Failed to start mysql.service: Unit mysql.service failed to load: No such file or directory.
```

CentOS 7需要使用systemctl命令，对于MariaDB用:

> systemctl start|stop|restart mariadb.service

# MariaDB状态
> systemctl status -l mariadb.service

```shell
[root@localhost bin]# systemctl status -l mariadb.service
● mariadb.service - MariaDB database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2016-01-21 16:24:03 CST; 7min ago
  Process: 14573 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID (code=exited, status=0/SUCCESS)
  Process: 14545 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n (code=exited, status=0/SUCCESS)
 Main PID: 14572 (mysqld_safe)
   CGroup: /system.slice/mariadb.service
           ├─14572 /bin/sh /usr/bin/mysqld_safe --basedir=/usr
           └─14730 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib64/mysql/plugin --log-error=/var/log/mariadb/mariadb.log --pid-file=/var/run/mariadb/mariadb.pid --socket=/var/lib/mysql/mysql.sock

Jan 21 16:24:01 localhost.localdomain systemd[1]: Starting MariaDB database server...
Jan 21 16:24:01 localhost.localdomain mysqld_safe[14572]: 160121 16:24:01 mysqld_safe Logging to '/var/log/mariadb/mariadb.log'.
Jan 21 16:24:01 localhost.localdomain mysqld_safe[14572]: 160121 16:24:01 mysqld_safe Starting mysqld daemon with databases from /var/lib/mysql
Jan 21 16:24:03 localhost.localdomain systemd[1]: Started MariaDB database server.
```