title: My centos virtual
date: 2016-01-20 09:48:35
tags: "linux"
---
> /etc/httpd/conf.d

```shell
nameVirtualHost 192.168.1.112:80

#<VirtualHost *:80>
#    ServerName mycake.com
#    DocumentRoot /var/www/html/cakephp/app/webroot
#</VirtualHost>

#<Directory "/var/www/html/cakephp/app/webroot">
#    Options FollowSymLinks
#    AllowOverride All
#    Require all granted
#</Directory>

<VirtualHost *:80>
    ServerName 192.168.1.112
    DocumentRoot /var/www/html
</VirtualHost>

<Directory "/var/www/html">
    Options FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

```
# 截图：
![My centos virtual](http://7xq5ds.com1.z0.glb.clouddn.com/MycentosVirtual.jpg)