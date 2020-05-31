---
title: 项目管理禅道Linux
date: 2019/05/05 12:07:14
tags: 
 - Linux
 - 项目管理
 - 学习
categories: 
 - 项目管理
---

## 

- **官网：**http://www.zentao.net/book/zentaopmshelp/90.html

- **下载安装**

  ```shell
  ##下载
  wget http://dl.cnezsoft.com/zentao/11.6.4/ZenTaoPMS.11.6.4.zbox_64.tar.gz
  ##解压
  tar -zxvf ZenTaoPMS.11.6.4.zbox_64.tar.gz 
  #移动解压后台文件/opt
  mv zbox /opt/
  #添加文件权限
  chmod 777 zbox/
  ```

  

- **配置端口**

  > ap参数 可以修改Apache的端口，-mp参数 可以修改Mysql的端口。
  > 例如（apache端口改为8080，mysql端口改为3307）：
  > /opt/zbox/zbox -ap 3307 -mp 3308
  
- **常用命令**

  > 执行/opt/zbox/zbox start 命令开启Apache和Mysql。 执行/opt/zbox/zbox stop 命令停止Apache和Mysql。 执行/opt/zbox/zbox restart 命令重启Apache和Mysql。 /opt/zbox/zbox -h命令来获取关于zbox命令的帮助。 ##如果需要开机自动启动，可以把 /opt/zbox/zbox restart 加到操作系统的自启目录。

- 登录

  > 启动 Apache和Mysql服务后， 浏览器直接访问：http://62.234.166.188:3307/zentao 注：如果网页无法访问，请先关闭禅道所在电脑的防火墙和selinux再刷新网页访问试一下。 禅道默认管理员帐号是 admin，密码 123456 首次登录后，修改admin密码

- **访问数据库**

  > 添加数据库(不执行此步骤无法访问数据库) sh /opt/zbox/auth/adduser.sh 登录数据库：/opt/zbox/bin/mysql -u root -P mysql端口 -p  （比如：/opt/zbox/bin/mysql -u root -P 3306 -p） 导入数据库：/opt/zbox/bin/mysql -u root -P mysql端口 -p 要导入的库名 < XXXX.sql （比如：/opt/zbox/bin/mysql -u root -P 3306 -p zentao < zentao.sql）