title: Centos7安装samba服务
date: 2016-04-01 09:55:49
tags: "linux"
---
<Excerpt in index | 首页摘要> 
<!-- more -->
<The rest of contents | 余下全文>
> https://download.samba.org/pub/samba/stable/

1、解压、编译、安装
```bash
tar -zxvf samba-4.4.0.tar.gz
cd samba-4.4.0
./configure --prefix=/usr/local/samba/
make 
make install
```
2、配置smb.conf，比较关键的一步

安装目录下有很多smb.conf的例子，但/usr/local/samba/lib下却没有，所以必须copy.
也可以用链接的方法：
创建配置文件 

```bash
mkdir  /usr/local/samba/etc 
vim    /usr/local/samba/etc/smb.conf 
```
编辑完配置文件后，保存。 
```bash
ln –s  /usr/local/samba/etc/smb.conf   /usr/local/samba/lib/smb.conf  
```

3、配置smb.conf
```bash
#vim smb.conf
```

检查smb.conf配置语法是否错误和设置是否成功
```bash
#/usr/local/samba/bin/testparm
```


[global]
        workgroup = MYGROUP
        server string = Samba Server Version %v
        log file = /var/log/samba/log.%m
        max log size = 50
        security = user
        map to guest = Bad Password
        passdb backend = tdbsam
        load printers = yes
        cups options = raw

[html]
        path = /var/www/html/
        public = yes
        writable = yes




4、增加smb用户
比如有一个用户名是kitty,那么：
此用户必须为linux实际用户，如不存在使用 useradd命令

```bash
/usr/local/samba/bin/smbpasswd -a fhy
```
就把fhy增加为smb用户了。

5、启动smb服务
```bash
#/usr/local/samba/sbin/smbd -D
#/usr/local/samba/sbin/nmbd -D
```
6、设为开机启动
```bash
vim /etc/rc.d/rc.local
```
加入 并设为可执行
```bash
#!/bin/bash
/usr/local/samba/sbin/smbd -D
/usr/local/samba/sbin/winbindd -D
/usr/local/samba/sbin/nmbd -D
```

# 添加防火墙规则
```bash
[root@localhost ~]# firewall-cmd --zone=public --add-port=137/udp --permanent
success
[root@localhost ~]# firewall-cmd --zone=public --add-port=138/udp --permanent
success
[root@localhost ~]# firewall-cmd --zone=public --add-port=139/tcp --permanent
success
[root@localhost ~]# firewall-cmd --zone=public --add-port=445/tcp --permanent
success
[root@localhost ~]# firewall-cmd --reload
```