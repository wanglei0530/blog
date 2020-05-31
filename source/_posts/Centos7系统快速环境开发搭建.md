---
title: Centos7系统快速环境开发搭建
date: 2019/06/12 01:16:34
tags: 
 - Linux 
 - 服务器 
 - Centos7


---



系统安装略过，很简单！
需要注意的是：确定需要安装 精简版(纯命令)和桌面版！
阿里巴巴镜像库：https://opsx.alibaba.com

### 网络配置

```shell
#进入配置目录
vi /etc/sysconfig/network-scripts/ifcfg-ens33
#修改noboot为“yes”
:wq
#重启网络
service network restart
```

### 安装基础命令(已装忽略)

```shell
#安装ipconfig命令 如果已安装请忽略
yum install net-tools -y
#安装wget命令
yum install wget -y
#brew mac软件源下载卡住问题
vim ~/.bash_profile
#新增一行
export HOMEBREW_NO_AUTO_UPDATE=true
```

### 远程ssh配置

```shell
#安装ssh服务
yum -y install openssh
#修改ssh配置
vim /etc/ssh/sshd_config
#PermitRootLogin yes
#RSAAuthentication yes 
#PubkeyAuthentication yes
ssh root@192.168.4.199
```

### firewalld防火墙配置

```shell
#下载安装
yum install -y firewalld
yum install -y firewall-config 
#查看firewall的状态
firewall-cmd --state
#启动
systemctl start firewalld
#关闭
systemctl stop firewalld 
#查看firewalld当前状态
systemctl status firewalld 
#开机启动
systemctl enable firewalld 
#取消开机启动
systemctl disable firewalld 
#查看开放端口命令
firewall-cmd --list-ports
#添加开放端口
firewall-cmd --permanent --zone=public --add-port=3306/tcp
firewall-cmd --zone=public --add-port=5000/tcp --permanent  #对外网全部开放
firewall-cmd --zone=public --permanent --add-rich-rule 'rule family=ipv4 source address=192.168.0.1/24 port port=22 protocol=tcp accept' #添加端口开放
firewall-cmd --permanent --remove-rich-rule 'rule family=ipv4 source address=192.168.0.1/2 port port=5000 protocol=tcp accept' #删除端口开放
```

### iptables-services(防火墙,非必须-可以解决mysql无法远程连接问题)

```shell
#查询是否已安装
rpm -qa|grep -i iptables
#安装
yum install iptables-services
#启动防火墙
systemctl enable iptables
systemctl start iptables
#查看状态
service iptables status
#打开&关闭
service iptables start 
service iptables stop
#开放端口(3306端口距离)
vim /etc/sysconfig/iptables
#添加  -A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
#重启防火墙
systemctl restart iptables
#设置为系统服务，开机自启动
systemctl enable iptables.service
```

### 配置静态IP地址(防止总是自动修改)

```shell
vim /etc/sysconfig/network-scripts/ifcfg-enp0s3
#----------------修改为以下配置---------------#
TYPE="Ethernet"
BOOTPROTO="static" #静态方式
NM_CONTROLLED="no" #不使用网络管理器
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp0s3"
UUID="2b4bcec5-6c11-411b-b7b5-177d6b1075b2"
DEVICE="enp0s3"
ONBOOT="yes"


TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="dhcp"
NM_CONTROLLED="no"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="em1"
UUID="a7e4500e-47f5-4df2-828b-205df54500f0"
DEVICE="em1"
ONBOOT="yes"
HWADDR="e4:54:e8:c8:6c:31"
#设置固定ip
IPADDR=192.168.4.199 
#子网掩码为物理机子网掩码(subnet mask)
NETMASK=255.255.255.0 
#设置网关为物理机网关(gateway)
GATEWAY=192.168.4.1 
#----------------配置完成！---------------#
#重启网卡
service network restart
#----------------添加DNS服务器(非必须！)---------------#
vim /etc/resolv.conf
```

## 安装开发软件

### 安装mysql

```shell
#查询之前是否安装过MySQL
rpm -qa|grep -i mysql
#卸载旧版本MySQL
rpm -e --nodeps  软件名称(上面查找出来的MySQL旧版本)
#下载安装mysql wget 资源库
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
#下载安装mysql yum 资源库
yum -y install mysql57-community-release-el7-10.noarch.rpm
#安装mysql服务器
yum -y install mysql-community-server
#获取mysql创建的随机密码
grep "password" /var/log/mysqld.log
#添加可连接授权
grant replication slave on *.* to 'root'@'%' identified by '123456';
flush privileges;
```

### 安装Mycat

```shell
#查询之前是否安装过MyCat
rpm -qa|grep -i mysql
#下载MyCat
wget http://dl.mycat.io/1.6-RELEASE/Mycat-server-1.6-RELEASE-20161028204710-linux.tar.gz
tar -zvxf Mycat-server-1.5.1-RELEASE-20160602230303-linux.tar.gz 
#配置环境变量(非必要)
export MYCAT_HOME=/usr/local/mycat
source /etc/profile
#测试是否生效：
echo $MYCAT_HOME
```

### 安装JavaJDK

```shell
yum install java java-1.8.0-openjdk-devel
#环境变量
whereis java 
ll /usr/lib/jvm
vim /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.241-2.6.20.0.el7_7.x86_64
export JRE_HOME=${JAVA_HOME}
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
source /etc/profile
java-version
```

### 安装Maven环境变量配置

```shell
#下载
https://maven.apache.org/download.cgi
wget https://www-eu.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar -zxf apache-maven-3.6.1-bin.tar.gz 
#环境变量
vim /etc/profile
vim ~/.bash_profile 
export MAVEN_HOME=/usr/java/dev_tools/apache-maven-3.6.3
export PATH=$MAVEN_HOME/bin:$PATH
#测试
source /etc/profile
mvn -version
```

### 安装git配置

```shell
#下载
yum install git 
#配置git账号密码(非必须)
git config --global user.name [username]
git config --global user.password [userpassword]
git config --global credential.helper store
```

### 部署Jar项目脚本

```shell
#! /bin/bash
echo "测试环境[app]启动部署，使用pipeline分支"
sleep 3s;
cd /root/klks-cloud-source/klks-cloud-platform/
git checkout dev
git pull
mvn clean install -Dmaven.test.skip=true
cd  /usr/java/klks-cloud/
PID=`ps -ef |grep "klks-app" |grep -v "grep" | awk '{print $2}'`
echo $PID
kill -9 $PID
sleep 5s
rm klks-app-1.0.0.jar -rf 
cp /root/klks-cloud-source/klks-cloud-platform/klks-app/target/klks-app-1.0.0.jar .
nohup java -jar -Dspring.profiles.active=dev klks-app-1.0.0.jar >> app.log & tail -f app.log
```

