
容器的集群

# Docker Compose

-----

### 简介：

Docker compose 可以用来轻松高效的管理容器，定义多个容器的启动。

> 官方介绍

Docker Compose 是一个定义和运行多容器应用程序的工具。它是解锁精简高效的开发和部署体验的关键。

Compose 简化了整个应用程序堆栈的控制，让您能够轻松地在一个简单易懂的 YAML 配置文件中管理服务、网络和卷。然后，您只需使用一个命令即可从配置文件中创建和启动所有服务。

Compose 适用于所有环境；生产、准备、开发、测试以及 CI 工作流。它还具有用于管理应用程序整个生命周期的命令：

- 启动、停止和重建服务
- 查看正在运行的服务的状态
- 流式输出正在运行的服务的日志
- 在服务上运行一次性命令

> 自己的理解

Compose是Docker官方的开源软件，需要自己安装！

Dockerfile 让程序在任何地方运行。 web服务、redis、mysql、nginx

Compose 可以将几个容器同时启动

##### Compose:

```yaml
version: '2.0'
services:
  web:
	build: .
	ports:- "5000:5000"
	volumes:- .:/code- logvolume01:/var/log
	links:- redis
  redis:
 	image: redis
volumes:
  logvolume01: {}
```

### 安装docker-compose：

[[官方安装文档](https://docs.docker.com/compose/install/)](https://docs.docker.com/compose/install/)

```shell
# 1.安装
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

# 2.赋予文件可执行权限
[root@ip-10-0-1-206 bin]# chmod +x docker-compose 

# 3.查看是否安装成功
[root@ip-10-0-1-206 bin]# docker-compose version
docker-compose version 1.22.0, build f46880fe
docker-py version: 3.4.1
CPython version: 3.6.6
OpenSSL version: OpenSSL 1.1.0f  25 May 2017

```



### 快速入门：

[[Docker Compose Quickstar](https://docs.docker.com/compose/gettingstarted/)](https://docs.docker.com/compose/gettingstarted/)

这是官方给出的一个快速体验docker compose的一个教程，基于的是一个python的项目

基本 Python Web 应用程序的开发来介绍 Docker Compose 的基本概念

```shell
[root@ip-10-0-1-206 composetest]# docker-compose up
Creating network "composetest_default" with the default driver
Building web
Step 1/10 : FROM python:3.10-alpine
3.10-alpine: Pulling from library/python
38a8310d387e: Pull complete
.....

redis_1  | 1:M 10 Dec 2024 01:37:59.127 * Running mode=standalone, port=6379.
redis_1  | 1:M 10 Dec 2024 01:37:59.127 * Server initialized
redis_1  | 1:M 10 Dec 2024 01:37:59.127 * Ready to accept connections tcp
.....
web_1    |  * Running on http://127.0.0.1:5000
web_1    |  * Running on http://172.18.0.3:5000
web_1    | Press CTRL+C to quit
web_1    |  * Restarting with stat
web_1    |  * Debugger is active!
web_1    |  * Debugger PIN: 100-133-274

```



### Dockerfile.yaml规则

















# Docker Swarm









# Docker secret









# Docker Config









# K8s