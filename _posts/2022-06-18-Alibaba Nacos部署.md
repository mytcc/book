---
layout: post
title:  Alibaba Nacos部署
categories: [Nacos]
excerpt: Alibaba Nacos 比 Eureka强大很多，并且现在很多企业都把Nacos当作了服务发现的首选。
---


#### Alibaba Nacos 概念解读

**服务注册中心**：它是服务，实例及原数据的数据库；服务注册中心可能会调用服务实例的健康检查API来验证它是否能够处理请求。  
**服务元数据**：包含服务端点（endpoints）、服务标签、服务版本号、服务实例权重、路由规则、安全策略等描述服务的数据。  
服务提供方、消费方：提供可复用和可调用服务的应用方;会发起对某个服务调用的应用方。  
**配置**：在系统开发过程中通常会将一些需要变更的参数、变量等从代码中分离出来独立管理，以独立的配置文件的形式存在。


#### 单机版本部署步骤

下载所需的版本：https://github.com/alibaba/nacos/releases

windows 单机启动
```shell
startup.cmd -m standalone
```

linux 单机启动
```shell
startup.sh -m standalone
```
启动后日志如下：

```bash
PS D:\software\nacos\bin> .\startup.cmd -m standalone
"nacos is starting with standalone"

         ,--.
       ,--.'|
   ,--,:  : |                                           Nacos 2.1.0
,`--.'`|  ' :                       ,---.               Running in stand alone mode, All function modules
|   :  :  | |                      '   ,'\   .--.--.    Port: 8848
:   |   \ | :  ,--.--.     ,---.  /   /   | /  /    '   Pid: 11552
|   : '  '; | /       \   /     \.   ; ,. :|  :  /`./   Console: http://192.168.1.107:8848/nacos/index.html
'   ' ;.    ;.--.  .-. | /    / ''   | |: :|  :  ;_
|   | | \   | \__\/: . ..    ' / '   | .; : \  \    `.      https://nacos.io
'   : |  ; .' ," .--.; |'   ; :__|   :    |  `----.   \
|   | '`--'  /  /  ,.  |'   | '.'|\   \  /  /  /`--'  /
'   : |     ;  :   .'   \   :    : `----'  '--'.     /
;   |.'     |  ,     .-./\   \  /            `--'---'
'---'        `--`---'     `----'

2022-06-18 13:52:52,459 INFO Bean 'org.springframework.security.access.expression.method.DefaultMethodSecurityExpressionHandler@263f04ca' of type [org.springframework.security.access.expression.method.DefaultMethodSecurityExpressionHandler] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)

2022-06-18 13:52:52,469 INFO Bean 'methodSecurityMetadataSource' of type [org.springframework.security.access.method.DelegatingMethodSecurityMetadataSource] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)

2022-06-18 13:52:52,893 INFO Tomcat initialized with port(s): 8848 (http)

2022-06-18 13:52:53,359 INFO Root WebApplicationContext: initialization completed in 3778 ms

2022-06-18 13:52:57,496 INFO Initializing ExecutorService 'applicationTaskExecutor'

2022-06-18 13:52:57,589 INFO Adding welcome page: class path resource [static/index.html]

2022-06-18 13:52:57,855 INFO Creating filter chain: Ant [pattern='/**'], []

2022-06-18 13:52:57,888 INFO Creating filter chain: any request, [org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@732c9b5c, org.springframework.security.web.context.SecurityContextPersistenceFilter@5ae95707, org.springframework.security.web.header.HeaderWriterFilter@68217d41, org.springframework.security.web.csrf.CsrfFilter@43f61afb, org.springframework.security.web.authentication.logout.LogoutFilter@30e6a763, org.springframework.security.web.savedrequest.RequestCacheAwareFilter@367b22e5, org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@3ae0b770, org.springframework.security.web.authentication.AnonymousAuthenticationFilter@38320e34, org.springframework.security.web.session.SessionManagementFilter@3e5d4f6b, org.springframework.security.web.access.ExceptionTranslationFilter@4fad6218]

2022-06-18 13:52:57,966 INFO Initializing ExecutorService 'taskScheduler'

2022-06-18 13:52:57,981 INFO Exposing 2 endpoint(s) beneath base path '/actuator'

2022-06-18 13:52:58,055 INFO Tomcat started on port(s): 8848 (http) with context path '/nacos'

2022-06-18 13:52:58,055 INFO Nacos started successfully in stand alone mode. use embedded storage

2022-06-18 13:53:17,500 INFO Initializing Servlet 'dispatcherServlet'

2022-06-18 13:53:17,510 INFO Completed initialization in 10 ms
```

`可以在conf目录下 application.properties 文件修改配置，通常采用MySQL作为持久化数据库，nacos-mysql.sql 文件用于初始化数据库。`

启动后访问：http://localhost:8848/nacos/index.html ，用户名密码：nacos/nacos 即可访问，如下：
![nacosindex](/images/2022/06/nacosindex.png)


#### 集群化部署
1、定义集群中所有部署机器的的ip和端口，即cluster.conf 文件，如下：
```
192.168.16.101:8847
192.168.16.102:8847
192.168.16.103:8847
```
2、集群必须要使用可以共同访问到的数据源作为持久化的方式。
```bash
### If use MySQL as datasource:
spring.datasource.platform=mysql
### Count of DB:
db.num=1
### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=nacos
db.password.0=nacos
```
3、集群化启动没有额外的参数 startup.sh

在页面中查看服务节点信息如下：
![nodelist](/images/2022/06/nodelist.png)
