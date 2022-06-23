---
layout: post
title:  带你认识Flowable UI
categories: [flowable]
excerpt: Flowable提供了几个web应用，用于演示及介绍Flowable项目提供的功能，FlowableIDM:身份管理应用，FlowableModeler:让具有建模权限的用户可以创建流程模型、表单、选择表与应用定义，FlowableTask:运行时任务应用FlowableAdmin:管理应用。
---
&emsp;&emsp;Flowable提供了几个web应用，用于演示及介绍Flowable项目提供的功能：
- Flowable IDM: 身份管理应用。为所有Flowable UI应用提供单点登录认证功能，并且为拥有IDM管理员权限的用户提供了管理用户、组与权限的功能。
- Flowable Modeler: 让具有建模权限的用户可以创建流程模型、表单、选择表与应用定义。
- Flowable Task: 运行时任务应用。提供了启动流程实例、编辑任务表单、完成任务，以及查询流程实例与任务的功能。
- Flowable Admin: 管理应用。让具有管理员权限的用户可以查询BPMN、DMN、Form及Content引擎，并提供了许多选项用于修改流程实例、任务、作业等。管理应用通过REST API连接至引擎，并与Flowable Task应用及Flowable REST应用一同部署。

&emsp;&emsp;所有其他的应用都需要Flowable IDM提供认证。每个应用的WAR文件可以部署在相同的servlet容器（如Apache Tomcat）中，也可以部署在不同的容器中。由于每个应用使用相同的cookie进行认证，因此应用需要运行在相同的域名下。



### 2.1 安装部署

下载Tomcat：https://tomcat.apache.org/download-80.cgi 官网下载后解压缩到非中文目录即可，然后是下载FlowableUI的文件，在Flowable6.6之后把FlowableUI中提供的四个功能合并到了一起。

![image-20220318102128672](/images/2022/06/image-20220318102128672.png)

然后把解压缩后的两个war包拷贝到Tomcat的解压缩的webapps目录下

![image-20220318102224330](/images/2022/06/image-20220318102224330.png)

Tomcat目录：

![image-20220318102255188](/images/2022/06/image-20220318102255188.png)



### 2.2 启动服务

&emsp;&emsp;启动Tomcat服务，执行startup.bat文件

![image-20220318102325924](/images/2022/06/image-20220318102325924.png)

如果启动中出现乱码修改Tomcat的conf目录下的 logging.properties 文件中的编码

![image-20220318102446699](/images/2022/06/image-20220318102446699.png)



如果一闪而过则检查jdk的环境变量配置。启动成功后，在浏览器中访问 http://localhost:8080/flowable-ui, 默认的账号密码是 admin/test

![image-20220318102609807](/images/2022/06/image-20220318102609807.png)



### 2.3 用户管理

&emsp;&emsp;我们先在 `身份管理应用程序` 中创建用户并授权

![image-20220318102707368](/images/2022/06/image-20220318102707368.png)

创建用户

![image-20220318102734238](/images/2022/06/image-20220318102734238.png)





填写详细信息

![image-20220318102817782](/images/2022/06/image-20220318102817782.png)



授权管理

![image-20220318102859814](/images/2022/06/image-20220318102859814.png)





### 2.4 绘制流程



创建新的流程

![image-20220318101239742](/images/2022/06/image-20220318101239742.png)

流程图界面

![image-20220318101346692](/images/2022/06/image-20220318101346692.png)





创建流程，分配处理人

![image-20220318101639424](/images/2022/06/image-20220318101639424.png)





![image-20220318101611564](/images/2022/06/image-20220318101611564.png)

继续完成流程图的创建

![image-20220318101810272](/images/2022/06/image-20220318101810272.png)





### 2.5 部署流程

&emsp;&emsp;绘制好的流程图，我们只需要一键导出即可

![image-20220318103413285](/images/2022/06/image-20220318103413285.png)

下载下来后拷贝到项目的resource目录下即可

![image-20220318103518807](/images/2022/06/image-20220318103518807.png)





然后就是正常的操作流程了





### 2.6 FlowableUI 演示

#### 2.6.1 部署流程

&emsp;&emsp;在FlowableUI中提供了演示程序

![image-20220318104517967](/images/2022/06/image-20220318104517967.png)

创建一个新的应用程序，并指定相关的信息

![image-20220318104614784](/images/2022/06/image-20220318104614784.png)

创建应用后需要指定对应的流程图

![image-20220318104703306](/images/2022/06/image-20220318104703306.png)



![image-20220318104735714](/images/2022/06/image-20220318104735714.png)





![image-20220318104811812](/images/2022/06/image-20220318104811812.png)



发布应用程序

![image-20220318105045345](/images/2022/06/image-20220318105045345.png)







#### 2.6.2 启动流程

&emsp;&emsp;发布了应用程序后我们就可以来启动流程了

![image-20220318105258331](/images/2022/06/image-20220318105258331.png)



![image-20220318105315908](/images/2022/06/image-20220318105315908.png)





![image-20220318105336107](/images/2022/06/image-20220318105336107.png)



![image-20220318105420011](/images/2022/06/image-20220318105420011.png)

点击显示图：

![image-20220318105444672](/images/2022/06/image-20220318105444672.png)

也就是可以看到当前是user1来处理，user1登录后可以看到要处理的流程，user2登录是看不到的。

![image-20220318105646273](/images/2022/06/image-20220318105646273.png)



点击完成后流程就向下一步流转了

![image-20220318105727192](/images/2022/06/image-20220318105727192.png)



这时再通过user2登录，就可以看到对应的代办的信息

![image-20220318105808830](/images/2022/06/image-20220318105808830.png)



![image-20220318105848185](/images/2022/06/image-20220318105848185.png)

然后点击完成，那么整个流程就介绍了
