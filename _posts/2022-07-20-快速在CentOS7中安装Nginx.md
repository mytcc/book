---
layout: post
title:  快速在CentOS7中安装Nginx
categories: [markdown]
excerpt: 如何在CentOS7系统下快速安装Nginx，通过图文并茂的形式给大家展示，感兴趣的朋友一起看看吧.
---
#### 一、概述

nginx是我们在开发Java项目时，必不可少的一个工具，大多数时候用于反向代理后端服务器，达到负载均衡、解决高并发的目的。  
在大多数时候，Nginx的安装和配置是由运维人员完成的，但在一些没有运维岗位的公司，还是需要开发人员来做这项工作。  
今天我们就只图快不图细的讲解一下如何在CentOS7系统下快速安装Nginx。

#### 二、下载Nginx安装包

1）进入Nginx官网（https://nginx.org/），点击 download 链接
![nginxinstall](/images/2022/07/nginxinstall.png)
2）在 Stable version 中，下载最新版本  
![latest](/images/2022/07/latest.png)


#### 三、安装依赖包
在CentOS7命令行模式下，依次输入以下命令，安装所需的依赖包
```shell
yum install -y gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
```
![install1](/images/2022/07/install1.png)
![install2](/images/2022/07/install2.png)


#### 四、将Nginx安装包拷贝到CentOS7系统并解压
将安装包拷贝到CentOS7系统有很多方法，可以用Xshell工具连接CentOS7系统后使用 rz 命令上传，也可以使用Xftp或者其他工具进行上传。  
这里我们将Nginx安装包放到 /home 目录下。  
进入 /home 目录  
输入 tar -zxvf nginx-1.20.1.tar.gz 命令，对安装包进行解压缩
![install3](/images/2022/07/install3.png)

#### 五、配置Nginx

进入解压后的Nginx文件夹  
 cd nginx-1.20.1

输入配置命令：
```shell
./configure
–prefix=/usr/local/nginx
–pid-path=/var/local/nginx/nginx.pid
–lock-path=/var/local/nginx/nginx.lock
–error-log-path=/var/local/nginx/error.log
–http-log-path=/var/local/nginx/access.log
–with-http_gzip_static_module
–http-client-body-temp-path=/var/local/nginx/client
–http-proxy-temp-path=/var/local/nginx/proxy
–http-fastcgi-temp-path=/var/local/nginx/fastcgi
–http-uwsgi-temp-path=/var/local/nginx/uwsgi
–http-scgi-temp-path=/var/local/nginx/scgi
```
![install4](/images/2022/07/install4.png)
![install5](/images/2022/07/install5.png)

#### 六、编译并安装
编译，输入命令：# make  
安装，编译完成后，输入命令： # make install  


#### 七、启动Nginx
进入到Nginx目录：# cd /usr/local/nginx/sbin  
输入启动命令：# ./nginx  
此时使用浏览器访问CentOS7服务器的IP地址，可以看到Nginx的首页。  
![nginxindex1](/images/2022/07/nginxindex1.png)

#### 八、Nginx的常用命令
Nginx的强行停止命令：　　　　# ./nginx -s stop  
Nginx的优雅停止命令：　　　　# ./nginx -s quit // 优雅停止，是等最后一次交互执行完再停止。  
Nginx检查配置文件是否有错： # ./nginx -t  
Nginx的重新加载命令：　　　 # ./nginx -s reload  
查看Nginx版本：　　　　　　 # ./nginx -v  
查看Nginx详细版本：　　　　 # ./nginx -V  
