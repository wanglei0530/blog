---
title: SpringBoot-Mycat（MySql读写分离）
date: 2019/05/05 12:07:14
---

[TOC]



- [ ] ## Mysql配置

### 1、环境信息

```
操作系统:centos7.0
#mysql版本 mysql-community-client-5.7.25-1.el6.x86_64.rpm
#节点 192.168.4.182(主)、192.168.4.183(从)
```

### 2、主数据库配置(my.cnf)

```less
vim /etc/my.cnf
#日志文件名称
log-bin=master-a-bin #二进制日志的格式，有row、statement和mixed三种类型 
binlog-format=ROW #要求各个服务器的这个id必须不一样
server-id=1
#同步的数据库名称
binlog-do-db=yidiankt
```

### 3、mysql执行,账号授权(让从库可以连接到)

```shell
mysql> grant replication slave on *.* to 'root'@'192.168.4.199' identified by '123456';
flush privileges;
#ERROR 1819 (HY000): Your password does not satisfy the current policy requirements(解决办法)
set global validate_password_length=6; 
set global validate_password_policy=LOW;
```

### 4、从数据库配置(my.cnf)

```shell
#日志文件名称
log-bin=master-a-bin
#二进制日志的格式，跟主服务器一样
binlog-format=ROW
#要求各个服务器的这个id必须不一样
server-id=2 #中继日志执行之后，这些变化是否需要计入自己的binarylog。当你的从服务器需要作为另外一个服务器的主服务器 的时候需要打开。就是双主互相备份，或者多主循环备份。
log-slave-updates=true
```

### 5、主数据库重启

```shell
service mysqld restart
mysql> show master status;
+---------------------+----------+--------------+------------------+-------------------+
| File                | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------------+----------+--------------+------------------+-------------------+
| master-a-bin.000002 |      154 |  slave_test  |                  |                   |
+---------------------+----------+--------------+------------------+-------------------+

```

### 6、切换到从库

```shell
service mysqld restart
#设置主从同步日志(在从库中执行！)
mysql> change master to 
master_host='192.168.4.189',
master_user='root',
master_password='123456',
master_port=3306,
master_log_file='master-a-bin.000010',
master_log_pos=1449;
#开启从数据库
mysql> start slave;
#查看状态(Slave_IO_Running,Slave_IO_Running都是yes才没问题，所以现在是有问题的！)
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: 
                  Master_Host: 192.168.4.199
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: master-a-bin.000002
          Read_Master_Log_Pos: 154
               Relay_Log_File: localhost-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: master-a-bin.000002
             Slave_IO_Running: No
            Slave_IO_Running: No
#添加从库连接权限
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
```

### 7、 测试主从同步！

```mysql
CREATE TABLE `user` (
  `id` int(11) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;
insert user values(id,name),(1,"a");
```

- [ ] ## MyCat配置

### 1、下载、安装、环境变量mycat

```shell
wget http://dl.mycat.io/1.6-RELEASE/Mycat-server-1.6-RELEASE-20161028204710-linux.tar.gz
tar -zvxf Mycat-server-1.5.1-RELEASE-20160602230303-linux.tar.gz 
#配置环境变量(非必须)
vi /etc/profile
#export MYCAT_HOME=/usr/local/mycat
source /etc/profile
#测试是否生效：
echo $MYCAT_HOME
```

### 2、配置mycat(server.xml)



```shell
vim mycat/conf/server.xml 
<!--system中添加UTF-8字符集设置，否则存储中文会出现问号 -->
<property name="charset">utf8</property>
<user name="root">
    <property name="password">123456</property>
    <property name="schemas">TESTDB</property>
</user>
<user name="user">
    <property name="password">123456</property>
    <property name="schemas">mycat</property>
    <property name="readOnly">true</property>
</user>
```

### 3、配置mycat(schema.xml)

```xml
vim mycat/conf/schema.xml
<!-- 配置节点 -->
<dataNode name="mycat_test" dataHost="localhost1" database="slave_test" />
<!-- balance参数说明: 0-不使用读写分离 1-使用读写分离 2-随机分配 -->
<dataHost name="localhost1" maxCon="1000" minCon="10" balance="1" writeType="0" dbType="mysql" dbDriver="native" switchType="1"  slaveThreshold="100">
    <heartbeat>select user()</heartbeat>
    <!-- 配置主节点(可以配置多个节点)-->
    <writeHost host="hostM1" url="192.168.4.199:3306" user="root" password="123456">
        <!-- 配合从节点(可以配置多个节点) -->
        <readHost host="hostS2" url="192.168.4.190:3306" user="root" password="123456" />
    </writeHost>
</dataHost>
<!-- 添加 dataNode="mycat_test" 否则不生效-->
<schema name="TESTDB" checkSQLschema="false" sqlMaxLimit="100" dataNode="mycat_test">
```

### 4、启动测试

```shell
./mycat/bin/mycat start
#查看是否启动
ps -ef | grep mycat
#登录mycat管理端口(一定要是管理端口！否则mycat命令无法使用)
mysql -uroot -p123456 -P9066 -h127.0.0.1
#-------------------mycat常用命令-------------------
#查看数据源的状态
mysql> show @@datasource;
#查看心跳状态
mysql> show @@heartbeat;
#用于更新 schema.xml 配置文件,免得重启服务
reload @@config;
#-----------------------------------------------
#修改日志级别
vim mycat/conf/log4j2.xml 
<asyncRoot level="info" includeLocation="true"> 修改为“debug”查看实时日志
#查看实时日志
tail -f mycat/logs/wrapper.log 
```

