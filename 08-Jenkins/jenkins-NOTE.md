# Jenkins NOTE

## Jenkins 简介

### DevOps 与 CI/CD

了解 Jenkins 前需要了解两个概念：`DevOps` 与 `CI/CD `

- DevOps

  在一般的软件公司的技术部门里，有两类常见的人员，开发和运维。开发负责编写业务代码把功能实现出来，运维负责把开发打包好的代码进行上线部署，还有服务器监控、故障排除等工作。

  在过去，多数应用都是大型的单体应用，以单个进程或几个进程的方式运行在服务器上。这些应用的开发周期较长，迭代速度不频繁。在新的应用发布日期到来之前，开发者会把新的代码打包好交给运维人员进行部署上线。

  如今，随着移动互联网时代的到来，互联网的用户与流量大幅增加，这种新情况要求软件企业以更快的速度迭代升级他们的产品和服务。软件企业为了适应新情况，纷纷把自己的软件服务拆解为多个小的、可独立运行的组件，我们称为“微服务”，为的就是能够短时间内对软件进行迭代升级 。这种微服务的快速迭代与以往传统的开发-运维相分离的合作方式就产生了矛盾。不断更新的服务需要运维人员不断地下线上线各种服务，可想而知，这样的效率及其低下，无法满足软件快速迭代的需求。为此，`DevOps ` 应运而生。



## Jenkins 安装

### Docker 容器运行

最简单的方法是使用 Docker 跑一个容器

```shell
# 执行 Docker 命令
docker run \
  --user root \
  --name jenkins \
  -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean
```

使用浏览器访问 `[host_ip]:8080` 即可访问

### 网页端配置

- 根据提示做好初始化操作

  <img src="/home/yym/桌面/learn/study_note/assets/01-jenkins-install-default-plugins.png" style="zoom: 25%;" />



## 参考

1. https://www.jianshu.com/p/5f671aca2b5a
2. https://www.jenkins.io/zh/doc/book/installing/