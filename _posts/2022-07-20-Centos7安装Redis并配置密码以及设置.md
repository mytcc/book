---
layout: post
title:  Centos7安装Redis并配置密码以及设置
categories: [redis]
excerpt: Centos7安装Redis并配置密码以及设置
---
#### 一、安装gcc依赖

```bash
yum install -y gcc
```

#### 二、下载并解压安装包
```shell
wget http://download.redis.io/releases/redis-5.0.3.tar.gz
tar -zxvf redis-5.0.3.tar.gz
```

#### 三、切换到redis解压目录下，执行编译

```shell
cd redis-5.0.3
make
```

#### 四、安装并指定安装目录
```shell
make install PREFIX=/usr/local/redis
```

#### 五、启动服务
**前台启动 **
```shell
cd /usr/local/redis/bin/
./redis-server
```
**后台启动**  
从 redis 的源码目录中复制 redis.conf 到 redis 的安装目录
```shell
root@localhost bin]# cp /usr/local/redis-5.0.3/redis.conf /usr/local/redis/bin/
#修改 redis.conf 文件，把 daemonize no 改为 daemonize yes
[root@localhost bin]# vi redis.conf
[root@localhost bin]# ./redis-server redis.conf
```


#### 六、设置开机启动
**添加开机启动服务**  
```shell
[root@localhost bin]# vi /etc/systemd/system/redis.service
```
复制粘贴以下内容：
```shell
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/redis/bin/redis-server /usr/local/redis/bin/redis.conf #改成自己的
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```
**设置开机启动**
```shell
systemctl daemon-reload
systemctl start redis.service
systemctl enable redis.service

ln -s /usr/local/redis/bin/redis-cli /usr/bin/redis #redis软连接
```
**服务操作命令**

```shell
systemctl start redis.service   #启动redis服务
systemctl stop redis.service   #停止redis服务
systemctl restart redis.service   #重新启动服务
systemctl status redis.service   #查看服务当前状态
systemctl enable redis.service   #设置开机自启动
systemctl disable redis.service   #停止开机自启动
```
这里记录一下，虚拟机Centos7安装好了Redis，启动也没什么问题，但是本地利用RedisDeskTopManagement一直连接不上，Redis默认设置的是允许本地连接。  

修改方式：  
修改redis.conf  
注释掉#bind 127.0.0.1  
添加 bind 0.0.0.0  

![redisinstall](/images/2022/07/redisinstall.png)

添加密码，找到下面这一行#requirepass foobared，去掉注释，并修改密码
![pwdcon](/images/2022/07/pwdcon.png)
