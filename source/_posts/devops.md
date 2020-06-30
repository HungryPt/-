---
title: Git + Jenkins + Docker 自动构建
categories: 
tags: DevOps
copyright: true
comments: true
description: 
date: 2019-02-15
thumbnail: /pic/3.jpg
disqusId: GitHub+Jenkins+Docker自动构建
---

---

由于每次更新博客部署起来太麻烦，心想可以自己搭建一套属于自己的devop环境，刚好之前也有接触过，但是没有自己亲手搭建，刚好自己有这个需求。

---
<!-- more -->

### 什么是DEVOPS
DevOps: Development和Operations的组合，是一种重视“软件开发人员（Dev）”和“IT运维技术人员（Ops）”之间沟通合作的文化、运动或惯例。透过自动化“软件交付”和“架构变更”的流程，来使得构建、测试、发布软件能够更加地快捷、频繁和可靠

### 1.Docker安装

Docker安装比较简单（win10建议是win10专业版）
- [windows安装](https://docs.docker.com/docker-for-windows/install/)
- [Mac安装](https://docs.docker.com/docker-for-mac/install/)

### 2.仓库配置

- 配置源码管理->Git，此处填写代码托管平台地址（GitHub ， Gitlab 等）和对应的Credentials
- 配置源码管理->Git，此处填写代码托管平台地址（GitHub ， Gitlab 等）和对应的Credentials
- 配置源码管理->Git，此处填写代码托管平台地址（GitHub ， Gitlab 等）和对应的Credentials
- 配置源码管理->Git，此处填写代码托管平台地址（GitHub ， Gitlab 等）和对应的Credentials


### 3.Jenkins配置

- 通过在Docker Hub中找到[Jenkins](https://hub.docker.com/_/jenkins)的镜像,并启动

```shell
docker run -p 8080:8080 -p 50000:50000 -v /your/home:/var/jenkins_home jenkins
```

  ![运行Jenkins](../../../../pic/1562573310.jpg)
  
- 设置账户后密码后登陆

  ![登陆](../../../../pic/1234.png)

- **安装需要的插件**
```
Maven Intergration plugin #用于构建maven项目的插件
Checkstyle plugin #检测代码的格式是否规范的插件
Findbugs plugin #对提交的代码静态检测，静态语法
Deploy to Container Plugin #将项目部署到Tomcat中需要用到的插件 （或者其他容器）
Publish over SSH plugin #需要部署项目到远程linux时需要到的插件
Gogs plugin #使用web钩子推送时，必须下载此插件，不然就会报403错误
Hudson SCP publisher plugin：拷贝部署文件到远程虚拟机
Publish Over SSH：执行远程部署命令
Environment Injector Plugin：注入变量
```
- **系统设置-Publish over SSH**
![SSH配置](../../../../pic/dd.png)

  - name：自定义
  - Hostname:远程服务器的ip
  - Usename:用户名
  - Remote Directory：上传文件的路径
  - 密码需要勾选才会显示，然后填入。
  - Port：端口根据实际情况配置。保存！

- **配置-增加构建后操作步骤。**
![SSH配置](../../../../pic/1551051-8ea42cdf1ecac92e.png)
  - Name:选择配置好的远程机器
  - Source files： 默认位置是项目的工作目录，所以要写上需要远程传输的文件的相对地址。
  - Remove prefix：去掉war包前面的文件夹
  - Remote directory： 这个目录要传送war包到目标服务器的目录，系统设置里面的Remote Directory 拼接上此处的 - Remote directory 形成的目录才是 war包最终的上传目录。  
  - 立即构建 构建成功



  ![点击立即构建](../../../../pic/wqe.png)



四、远程上传下载镜像
- **Exec command**
 远程服务器上需要执行的脚本的位置。可以在该脚本中写部署启动脚本。

- Execute shell中做好镜像后，不是在本机启动， 而是上传到镜像仓库。
```

#!/bin/bash
dates=`date "+%Y%m%d"`
projects="preview"
registry="192.168.22*.**:8088"
sudo  rm  -rf  /opt/product/preview/apps/preview/*
sudo cp  -rf  /var/lib/jenkins/workspace/preview/WebContent/*  /opt/product/preview/apps/preview/
sudo docker stop $projects  || true
sudo docker rm -f $projects || true
sudo docker rmi $registry/$projects:$dates || true
sudo docker build -t $registry/$projects:$dates  /opt/product/preview
#sudo docker run --name=$projects -d -ti -p 38001:8080 -v /opt/product/data:/opt/product/data -v /data/jdk:/data/jdk  $projects:$dates
sudo docker push  $registry/$projects:$dates
````
- 远程机器运行脚本位置

```
Exec command：/opt/test/gaoyx/remote/preview/preview.sh
脚本内容如下：
#!/bin/bash
dates=`date "+%Y%m%d"`
projects="preview"
registry="192.168.22*.**:8088"

sudo docker stop $projects  || true
sudo docker rm -f $projects || true
sudo docker rmi  $registry/$projects:$dates || true
sudo docker pull $registry/$projects:$dates
sudo docker run --name=$projects --restart=always -d -ti -p 6801:8080 -v  /opt/product/dat
```

<style>
hr{
    margin: 40px 0;
    height: 3px;
    border: none;
    background-color: #ddd;
    background-image: repeating-linear-gradient(-45deg, #fff, #fff 4px, transparent 4px, transparent 8px);
}
p{
  margin: 0 0 25px 0;
  font-size: 14px;
  line-height: 3;
  text-indent:20px;
  text-align: justify;
  /*letter-spacing:1px;*/
}
</style>