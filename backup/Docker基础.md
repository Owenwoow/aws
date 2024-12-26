
# Docker基础

## docker的安装和卸载

> 安装docker

根据官方文档安装



> docker 的 卸载

```shell
# 1、卸载依赖

# 1、卸载资源
rm -rf /var/lib/docker 

# /var/lib/docker docker 的默认存放路径
```





## 第一个docker应用

> 使用 sudo docker run hello-world





## docker的常用命令

### docker的帮助命令

```shell
docker version		# 显示docker的版本信息
docker info 		# 显示docker的系统级信息
docker 命令 --help   # 查看docker命令
```

帮助文档地址：https://docs.docker.com/reference/



### 镜像命令

##### **docker images**

```shell
[root@ip-10-0-1-102 ~]# docker images 
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    d2c94e258dcb   18 months ago   13.3kB


# 解释
REPOSITORY   镜像的仓库源
TAG			 镜像的标签
IMAGE ID     镜像的id
CREATED      镜像的创建时间
SIZE		 镜像的大小

# 可选项
  -a, --all             Show all images (default hides intermediate images)
  -q, --quiet           Only show image IDs

```



##### **docker search 命令搜索镜像%**

```shell
[root@kuangshen /]# docker search mysql
NAME                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql               MySQL is a widely used, open-source relation... 9494      [OK]       
mariadb             MariaDB is a community-developed fork of MyS... 3441      [OK]       

# 可选项，通过搜索来过滤
--filter=STARS=3000    # 搜索出来的镜像就是STARS大于3000的

[root@kuangshen /]# docker search mysql --filter=STARS=3000
NAME                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql               MySQL is a widely used, open-source relation... 9494      [OK]       
mariadb             MariaDB is a community-developed fork of MyS... 3441      [OK]       

[root@kuangshen /]# docker search mysql --filter=STARS=5000
NAME                DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql               MySQL is a widely used, open-source relation... 9494      [OK]

```



##### **docker pull 下载镜像**

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@kuangshen /]  # docker pull mysql
Using default tag: latest  # 如果不写 tag，默认就是 latest
latest: Pulling from library/mysql
5b54d594fba7: Pull complete
07e7d6a8a868: Pull complete
abd946892310: Pull complete
dd8f4d07efa5: Pull complete
07d6396a6205: Pull complete
cf6b2b93048f: Pull complete
530904b4a8b7: Pull complete
fb1e55059a95: Pull complete
4bd29a0dcde8: Pull complete
b94a001c6ec7: Pull complete
cb77cbeb422b: Pull complete
2a35cdbd42cc: Pull complete
Digest: sha256:dc255ca50a42b358197000b1f9bab2b4e01d158d1a9f56c3db6ee145506f625  # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  # 真实地址

# 等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载
[root@ip-10-0-1-102 ~]# docker pull mysql:8.0
8.0: Pulling from library/mysql
f1a9f94fc2db: Already exists   # 因为已经下载过了，所有没有下载。这就是docker分段下载的好处，省资源
7354d28ef157: Pull complete 
7478c7c1cef6: Pull complete 
ff2c12e6ea0c: Pull complete 
6fd1af2601dd: Pull complete 
562f9f9dbb9a: Pull complete 
9a6ec9901d5a: Pull complete 
2dd808bff6af: Pull complete 
397a7aefe9e4: Pull complete 
2300358455cf: Pull complete 
300abd503ad1: Pull complete 
Digest: sha256:f3a00e618edc1f7ea047d86527b3f3c4654b5e9e3d10d59e879f84a6c6cb110c
Status: Downloaded newer image for mysql:8.0
docker.io/library/mysql:8.0

```



##### **docker rmi 删除镜像**

```shell
[root@ip-10-0-1-102 ~]# docker rmi -f IMAGE ID						# 指定删除一个镜像

[root@ip-10-0-1-102 ~]# docker rmi -f IMAGE ID IMAGE ID IMAGE ID 	# 指定输出多个镜像

[root@ip-10-0-1-102 ~]# docker rmi -f $(docker images -aq)			# 删除所有镜像

```





### 容器基础命令

说明：有镜像才能创建容器，我们这里使用 CentOS 的镜像来测试，就是虚拟一个 CentOS！

```shell
docker pull centos
```

##### **新建容器并启动**

```shell
# 命令
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

# 常用参数说明
--name="Name"                # 给容器指定一个名字
-d                           # 后台方式运行容器，并返回容器的ID
-i                           # 以交互模式运行容器，通过和 -t 一起使用
-t                           # 给容器重新分配一个终端，通常和 -i 一起使用
-P                           # 随机端口映射（大写）
-p                           # 指定端口映射（小写），一般可以有四种写法：
      ip:hostPort:containerPort  (常用)
      ip::containerPort


# 测试
[root@kuangshen ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              470671670cac        3 months ago         237MB

# 使用 CentOS 以交互模式启动容器，在容器内执行 /bin/bash 命令！
[root@kuangshen ~]# docker run -it centos /bin/bash
[root@dc8f24dd06d0 /]# ls    # 注意地址，已经切换到容器内部了！
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

[root@dc8f24dd06d0 /]# exit  # 使用 exit 退出容器
exit
[root@kuangshen ~]#
```

##### 列出所有运行的容器

```shell
# 命令
docker ps [OPTIONS]
# 常用参数说明
-a      # 列出当前所有正在运行的容器 + 历史运行过的容器
-l      # 显示最近创建的容器
-n=?    # 显示最近n个创建的容器
-q      # 静默模式，只显示容器编号

[root@ip-10-0-1-102 ec2-user]# docker run -it centos /bin/bash
[root@27d70d250f3e /]# [root@ip-10-0-1-102 ec2-user]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
27d70d250f3e   centos    "/bin/bash"   13 seconds ago   Up 12 seconds             determined_knuth
```

##### 启动停止容器

```shell
docker start (容器id or 容器名)        # 启动容器
docker restart (容器id or 容器名)      # 重启容器
docker stop (容器id or 容器名)         # 停止容器
docker kill (容器id or 容器名)         # 强制停止容器
```

##### **退出容器**

```shell
exit         # 容器停止退出
ctrl+P+Q     # 容器不停止退出
```

##### 删除容器

```shell
docker rm 容器id                  # 删除指定容器
docker rm -f $(docker ps -a -q)  # 删除所有容器
docker ps -a -q|xargs docker rm  # 删除所有容器
```

##### 后台启动

```shell
# 命令
docker run -d 容器名
# 例子
docker run -d centos # 启动centos，使用后台方式启动
# 问题： 使用docker ps 查看，发现容器已经退出了！
# 解释：Docker容器后台运行，就必须有一个前台进程，容器运行的命令如果不是那些一直挂起的命
令，就会自动退出。
# 比如，你运行了nginx服务，但是docker前台没有运行应用，这种情况下，容器启动后，会立即自
杀，因为他觉得没有程序了，所以最好的情况是，将你的应用使用前台进程的方式运行启动。
```



### 容器常用的命令

##### 查看日志

```shell
# 命令
docker logs -f -t --tail 行数 容器id

# 例子：我们启动 centos，并编写一段脚本来测试玩玩！最后查看日志
[root@ip-10-0-1-102 ec2-user]# docker run -d centos /bin/sh -c "while true;do echo helloword;sleep 1;done"

[root@ip-10-0-1-102 ec2-user]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
43ebca898fb5   centos    "/bin/sh -c 'while t…"   7 seconds ago   Up 6 seconds             adoring_archimedes

# 现在我们的docker已经在后台运行了，可以去查看一下他的logs

# -t 显示时间戳  
# -f 打印最新的日志 
# --tail 数字  显示多少条！
[root@ip-10-0-1-102 ec2-user]# docker logs -tf --tail 5 43ebca898fb5
2024-11-19T08:45:30.923592687Z helloword
2024-11-19T08:45:31.925362836Z helloword
2024-11-19T08:45:32.926965962Z helloword
2024-11-19T08:45:33.929450739Z helloword
2024-11-19T08:45:34.931066410Z helloword

```

##### 查看容器中运行的进程信息

```shell
# 命令
docker top 容器id

# 测试
[root@kuangshen ~]# docker top c8530dbbe3b4
UID     PID     PPID    C     STIME     TTY   TIME        CMD
root    27437   27421   0     16:43    ?      00:00:00    /bin/sh -c ...

UID		# 当前的用户ID
PID		# 进程ID 

```

##### 查看容器/镜像的元数据

```shell
# 命令
docker inspect 容器ID

# 测试
[root@ip-10-0-1-102 ec2-user]# docker inspect 5b64776e1e1e
[
    {
        "Id": "5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189",
        "Created": "2024-11-19T08:57:44.360779006Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo helloword;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 878211,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2024-11-19T08:57:44.778343563Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189/hostname",
        "HostsPath": "/var/lib/docker/containers/5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189/hosts",
        "LogPath": "/var/lib/docker/containers/5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189/5b64776e1e1e529730a447510b84091cb78498d80c36f0caa187972fc5a44189-json.log",
        "Name": "/hardcore_cartwright",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                44,
                103
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [
                {
                    "Name": "nofile",
                    "Hard": 65536,
                    "Soft": 32768
                }
            ],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/c3fc8528a88b6fa134655d830a23098364ce4052ca9e6ee4ea9196d3712f766e-init/diff:/var/lib/docker/overlay2/dc3651a30756cf0bcf686de3a035a5c467586e4992ce60b611d154dc03b8b04a/diff",
                "MergedDir": "/var/lib/docker/overlay2/c3fc8528a88b6fa134655d830a23098364ce4052ca9e6ee4ea9196d3712f766e/merged",
                "UpperDir": "/var/lib/docker/overlay2/c3fc8528a88b6fa134655d830a23098364ce4052ca9e6ee4ea9196d3712f766e/diff",
                "WorkDir": "/var/lib/docker/overlay2/c3fc8528a88b6fa134655d830a23098364ce4052ca9e6ee4ea9196d3712f766e/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "5b64776e1e1e",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo helloword;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "340b871a1a828324a7567bc14c3beee779084881316146f93339e8697ac6b9ef",
            "SandboxKey": "/var/run/docker/netns/340b871a1a82",
            "Ports": {},
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "a9922b53314d099dda7dd0148c5f11657e58ceafb7496e154f3a9220b22678d1",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:11:00:02",
                    "NetworkID": "cf263bd4b0123b397f984b9abdecf47e1b8f10f37ea5d6cde0603ef0ac6c7548",
                    "EndpointID": "a9922b53314d099dda7dd0148c5f11657e58ceafb7496e154f3a9220b22678d1",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DriverOpts": null,
                    "DNSNames": null
                }
            }
        }
    }
]

```

##### 进入正在运行的容器

```shell
# 命令1
 docker exec -it 容器id bas


# 测试1
 [root@kuangshen ~]# docker ps
 CONTAINER ID        IMAGE               COMMAND                  CREATED     
        STATUS              PORTS               NAMES
 c8530dbbe3b4        centos              "/bin/sh -c 'while t…"   12 minutes 
ago      Up 12 minutes                           happy_chaum
 [root@kuangshen ~]# docker exec -it c8530dbbe3b4 /bin/bash
 [root@c8530dbbe3b4 /]# ps -ef
 UID        PID  PPID  C STIME TTY          TIME CMD
 root         1     0  0 08:43 ?        00:00:00 /bin/sh -c while true;do 
echo kuangshen;sleep
 root       751     0  0 08:56 pts/0    00:00:00 /bin/bash
 root       769     1  0 08:56 ?        00:00:00 /usr/bin/coreutils --
 coreutils-prog-shebang=s
 root       770   751  0 08:56 pts/0    00:00:00 ps -ef
 
```

##### 从容器内拷贝文件到主机上

```shell
# 命令
docker cp 容器id:容器内路径 目的主机路径

# 测试
# 容器内执行，创建一个测试文件传入主机
[root@ip-10-0-1-102 ~]# docker run -it centos /bin/bash

[root@05dc7a8c9a22 /]# cd /home
[root@05dc7a8c9a22 home]# vi 114514.txt
[root@05dc7a8c9a22 home]# ls -l
total 4
-rw-r--r--. 1 root root 7 Nov 22 01:20 114514.txt
[root@05dc7a8c9a22 home]# exit
exit

# linux复制查看，是否复制成功
[root@ip-10-0-1-102 ~]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                      
05dc7a8c9a22   centos    "/bin/bash"   About a minute ago   Exited (0) 23 seconds ago   .....

[root@ip-10-0-1-102 ~]# docker cp 05dc7a8c9a22:/home/114514.txt /root
                                               Successfully copied 2.05kB to /root
[root@ip-10-0-1-102 ~]# ll
total 4
-rw-r--r--. 1 root root 7 Nov 22 01:20 114514.txt

```



### 小结/常用命令合集

![image-20241122093619443](https://github.com/user-attachments/assets/a0921323-6e52-47e8-b1cb-102caeb3501f)

```shell
attach    Attach to a running container                        # 当前 shell 下 attach 连接指定运行镜像
build     Build an image from a Dockerfile                     # 通过 Dockerfile 定制镜像
commit    Create a new image from a container changes          # 提交当前容器为新的镜像
cp        Copy files/folders from the container's filesystem to the host path  # 从容器中拷贝指定文件或者目录到宿主机中
create    Create a new container                               # 创建一个新的容器，同 run，但不启动容器
diff      Inspect changes on a container's filesystem          # 查看 docker 容器变化
events    Get real time events from the server                 # 从 docker 服务获取容器实时事件
exec      Run a command in an existing container               # 在已存在的容器上运行命令
export    Stream the contents of a container as a tar archive  # 导出容器的内容流作为一个 tar 归档文件 [对应 import]
history   Show the history of an image                         # 展示一个镜像形成历史
images    List images                                          # 列出系统当前镜像
import    Create a new filesystem image from the contents of a tarball  # 从 tar 包中的内容创建一个新的文件系统映像 [对应 export]
info      Display system-wide information                      # 显示系统相关信息
inspect   Return low-level information on a container          # 查看容器详细信息
kill      Kill a running container                             # kill 指定 docker 容器
load      Load an image from a tar archive                     # 从一个 tar 包中加载一个镜像 [对应 save]
login     Register or Login to the docker registry server      # 注册或者登陆一个 docker 源服务器
logout    Log out from a Docker registry server                # 从当前 Docker registry 退出
logs      Fetch the logs of a container                        # 输出当前容器日志信息
port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT  # 查看映射端口对应的容器内部源端口
pause     Pause all processes within a container               # 暂停容器
ps        List containers                                      # 列出容器列表
pull      Pull an image or a repository from the docker registry server  # 从 docker 镜像源服务器拉取指定镜像或者库镜像
push      Push an image or a repository to the docker registry server  # 推送指定镜像或者库镜像至 docker 源服务器
restart   Restart a running container                          # 重启运行的容器
rm        Remove one or more containers                        # 移除一个或者多个容器
rmi       Remove one or more images                            # 移除一个或多个镜像 [无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]
run       Run a command in a new container                     # 创建一个新的容器并运行一个命令
save      Save an image to a tar archive                       # 保存一个镜像为一个 tar 包 [对应 load]
search    Search for an image on the Docker Hub                # 在 docker hub 中搜索镜像
start     Start a stopped containers                           # 启动容器
stop      Stop a running containers                            # 停止容器
tag       Tag an image into a repository                       # 给源中镜像打标签
top       Lookup the running processes of a container          # 查看容器中运行的进程信息
unpause   Unpause a paused container                           # 取消暂停容器
version   Show the docker version information                  # 查看 docker 版本号
wait      Block until a container stops, then print its exit code  # 截取容器停止时的退出状态值

```
### 作业

> 使用Docker 安装 Nginx

```shell
# 1.搜索镜像
[root@ip-10-0-1-102 ec2-user]# docker search nginx
NAME                                     DESCRIPTION                                     STARS     OFFICIAL
nginx                                    Official build of Nginx.                        20371     [OK]

# 2.下载镜像
[root@ip-10-0-1-102 ec2-user]# docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
2d429b9e73a6: Pull complete 
9b1039c85176: Pull complete 
9ad567d3b8a2: Pull complete 
773c63cd62e4: Pull complete 
1d2712910bdf: Pull complete 
4b0adc47c460: Pull complete 
171eebbdf235: Pull complete 
Digest: sha256:bc5eac5eafc581aeda3008b4b1f07ebba230de2f27d47767129a6a905c84f470
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest

# 3.启动镜像
[root@ip-10-0-1-102 ec2-user]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    60c8a892f36f   7 weeks ago   192MB
[root@ip-10-0-1-102 ec2-user]# 
[root@ip-10-0-1-102 ec2-user]# docker run -d --name testnginx -p 12312:80 nginx
9af12f2d8f19a1223df5942479f342c55ab677f2764359a221b65e8fe5a515c9
[root@ip-10-0-1-102 ec2-user]# 
[root@ip-10-0-1-102 ec2-user]# docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
9af12f2d8f19   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 5 seconds   0.0.0.0:12312->80/tcp,


# 本地验证
[root@ip-10-0-1-102 ec2-user]# curl localhost:12312
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>

# 进入容器查看html文件
[root@ip-10-0-1-102 ec2-user]# docker exec -it testnginx /bin/bash
root@9af12f2d8f19:/# 
root@9af12f2d8f19:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx

root@9af12f2d8f19:/# cd /usr/share/nginx
root@9af12f2d8f19:/usr/share/nginx# ls
html
root@9af12f2d8f19:/usr/share/nginx# ls -l
drwxr-xr-x. 2 root root 40 Nov 12 02:03 html
root@9af12f2d8f19:/usr/share/nginx# cd html/
root@9af12f2d8f19:/usr/share/nginx/html# ls -l
total 8
-rw-r--r--. 1 root root 497 Oct  2 15:13 50x.html
-rw-r--r--. 1 root root 615 Oct  2 15:13 index.html

root@9af12f2d8f19:/usr/share/nginx/html# cat index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
.....


root@9af12f2d8f19:/usr/share/nginx/html# exit
exit

```

思考问题：我们每次更nginx配置文件，都需要进入容器内部，有什么访问可以不进入容器而进行更改的吗？

答：可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改。       -v 数据卷



### 安装可视化docker管理界面

#### 部署

(https://docs.portainer.io/start/install-ce/server/docker/linux)

首先，创建 Portainer Server 用于存储其数据库的卷：

复制

```
docker volume create portainer_data
```

然后，下载并安装 Portainer Server 容器：

复制

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.21.4
```



默认情况下，Portainer 会生成并使用自签名 SSL 证书来保护端口。或者，您可以[[在安装期间](https://docs.portainer.io/advanced/ssl#using-your-own-ssl-certificate-on-docker-standalone)](https://docs.portainer.io/advanced/ssl#using-your-own-ssl-certificate-on-docker-standalone)或安装完成后[[通过 Portainer UI](https://docs.portainer.io/admin/settings#ssl-certificate)](https://docs.portainer.io/admin/settings#ssl-certificate)`9443`提供自己的 SSL 证书。



如果由于遗留原因需要`9000`开放 HTTP 端口，请将以下内容添加到`docker run`命令中：

```
-p 9000:9000
```

Portainer Server 现已安装。您可以通过运行以下命令检查 Portainer Server 容器是否已启动`docker ps`：

复制

```
root@server:~# docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED       STATUS      PORTS                                                                                  NAMES             
de5b28eb2fa9   portainer/portainer-ce:2.21.4  "/portainer"             2 weeks ago   Up 9 da
```



### 镜像commit

docker commit 从容器创建一个新的镜像

```shell
docker commit 提交容器副本使之成为一个新的镜像！
# 语法
docker commit -m="提交的描述信息" -a="作者" 容器id 要创建的目标镜像名:[标签名]

# 例子
# 我在centos的docker中添加了一个文件，想把它打包成新的镜像
[root@ip-10-0-1-102 ~]# docker commit -m="test centos" -a="Owen_W" 0677efe2f946 centos_test:1.0
sha256:64046bb43243d7b0e17214afaccded8cbdf91a89720873cef179a3bba555a1da

[root@ip-10-0-1-102 ~]# docker images -a
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
centos_test              1.0       64046bb43243   6 seconds ago   231MB
centos                   latest    5d0da3dc9764   3 years ago     231MB

```







## 容器数据卷

### 什么是容器数字卷

------

**docker的理念回顾：**

将应用和运行的环境打包形成容器运行，运行可以伴随着容器，但是我们对于数据的要求，是希望能够 持久化的！

就好比，你安装一个MySQL，结果你把容器删了，就相当于删库跑路了，这TM也太扯了吧！

所以我们希望容器之间有可能可以共享数据，Docker容器产生的数据，如果不通过docker commit 生成 新的镜像，使得数据作为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了！这样是行 不通的！

为了能保存数据在Docker中我们就可以使用卷！让数据挂载到我们本地！这样数据就不会因为容器删除 而丢失了！

**作用：**

卷就是目录或者文件，存在一个或者多个容器中，由docker挂载到容器，但不属于联合文件系统，因此 能够绕过 Union File System ， 提供一些用于持续存储或共享数据的特性： 

卷的设计目的就是数据的持久化，完全独立于容器的生存周期，因此Docker不会在容器删除时删除其挂 载的数据卷。



### 使用数据卷

> 方式一：容器中直接使用命令来添加

挂在：

```shell
# 命令
docekr run -it -v 主机地址:容器内地址 镜像名

# 测试
[root@ip-10-0-1-102 centos]# docker run -it -v /home/centos:/home/centos centos /bin/bash
```

查看数据卷是否挂载成功  `docker inspect 容器id`

![image-20241125153522050](https://github.com/user-attachments/assets/53a04493-d7bc-466f-918d-41a4b29cc560)


测试后发现主机和容器间可以实现共享。



### 匿名挂载和具名挂载

-----

#### 匿名挂载

> 含义

匿名挂载就是指定容器内路径，dockers run 每挂载一个目录就会宿主主机自动创建一个随机字符串的目录。目录`/var/lib/docker/volumes/`随机字符串。

![image-20241128095012980](https://github.com/user-attachments/assets/2396ecb5-1976-4873-b90c-2baae33a4368)


注意：里面的`_data`目录和对应容器内目录进行同步。

![image-20241128095135335](https://github.com/user-attachments/assets/511411f2-31ce-4ef4-82f1-b650efacfa42)




> 演示

```shell
# 运行容器
[root@ip-10-0-1-102 ec2-user]# docker run -d -p 8000:80 --name nginx001 -v /etx/nginx nginx
4365142585dbc134fa314b2fa452b644ef94dd28f31792387fc31344fb01f307
[root@ip-10-0-1-102 ec2-user]# 
[root@ip-10-0-1-102 ec2-user]# docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                   NAMES
4365142585db   nginx     "/docker-entrypoint.…"   43 seconds ago   Up 42 seconds   0.0.0.0:8000->80/tcp

# 查看挂在内容
[root@ip-10-0-1-102 ~]# docker volume ls
DRIVER    VOLUME NAME
local     b37fae70fcd294f28af151e259bc2657c031e110b2395ebd64e11c8a0e77313b
local     portainer_data

```



#### 具名挂载

> 含义

具名挂载就是挂载的同时指定一个有具体含义的名字，方便后续和容器对应以及后续的运维，便于查找挂载目录。



> 演示

```shell 
# 运行容器
[root@ip-10-0-1-102 volumes]# docker run -d -P --name nginx002 -v jumin-nginx:/etc/nginx nginx 
d5057ed2265531c816d78b4cd3ebf06c3ddebcb59f44e755e50d75eba8c25b0d

# 查看挂在内容
[root@ip-10-0-1-102 volumes]# docker volume ls
DRIVER    VOLUME NAME
local     b37fae70fcd294f28af151e259bc2657c031e110b2395ebd64e11c8a0e77313b
local     jumin-nginx
local     portainer_data
```





#### 拓展

> 改变文件的读写权限

```shell
# ro: readonly
# rw: readwrite
# 指定容器对我们挂载出来的内容的读写权限
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:rw nginx
```
### 初始dockerfile

-----

DockerFile 是用来构建Docker镜像的构建文件，是由一些列命令和参数构成的脚本。

测试：

> 构建镜像

```shell
# 1、我们在宿主机 /home 目录下新建一个 docker-test-volume文件夹
[root@kuangshen home]# mkdir docker-test-volume

# 说明：在编写DockerFile文件中使用 VOLUME 指令来给镜像添加一个或多个数据卷
VOLUME["/dataVolumeContainer1","/dataVolumeContainer2","/dataVolumeContainer
3"]

# 出于可移植和分享的考虑，我们之前使用的 -v 主机目录:容器目录 这种方式不能够直接在
DockerFile中实现。

# 由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有宿主机上都存在这样的特定目录.

# 2、编写DockerFile文件
[root@kuangshen docker-test-volume]# pwd
/home/docker-test-volume
[root@kuangshen docker-test-volume]# vim dockerfile1
[root@kuangshen docker-test-volume]# cat dockerfile1
# volume test
FROM centos
VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
CMD echo "-------end------"
CMD /bin/bash

# 3、build后生成镜像，获得一个新镜像 kuangshen/cento
docker build -f /home/docker-test-volume/dockerfile1 -t kuangshen/centos . #
注意最后有个.
```



> 启动容器

```shell
[root@ip-10-0-1-192 docker-test-volume]# docker run -it 90bc0d9dad3f /bin/bash 

[root@303222c13791 /]# ls -l
total 16
lrwxrwxrwx.   1 root root     7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   360 Nov 28 06:40 dev
drwxr-xr-x.   1 root root    66 Nov 28 06:40 etc
drwxr-xr-x.   2 root root     6 Nov  3  2020 home
........
drwxr-xr-x.   2 root root     6 Nov 28 06:40 volume01
drwxr-xr-x.   2 root root     6 Nov 28 06:40 volume02
```



>  查看是否挂在成功

```shell
# 进入文件创建一个文件进行测试
[root@621f81cbbc44 /]# cd volume01
[root@621f81cbbc44 volume01]# touch 123.txt

# 退出容器查看是否挂成功
[root@ip-10-0-1-192 docker-test-volume]# docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED                    
cbd0b3f210fb   90bc0d9dad3f   "/bin/sh -c /bin/bash"   6 minutes ago       

# 查看容器的详细信息
[root@ip-10-0-1-192 docker-test-volume]# docker inspect cbd0b3f210fb
```

![image-20241128145640984](https://github.com/user-attachments/assets/9473bd35-28f4-4430-bbb3-e0129e9a157d)


```shell
# 进入挂在容器的目录
[root@ip-10-0-1-192 docker-test-volume]# cd /var/lib/docker/volumes/79f160f1d792c8177fd304862bafc5aeffe676e9a0cd35e900c9052b05e3d1dc/_data
[root@ip-10-0-1-192 _data]# ll
-rw-r--r--. 1 root root 0 Nov 28 06:59 123.txt
```

同步成功



### 数据卷容器

-----

#### 实现容器和容器之间的数据同步

> 实验：开启三个容器进行测试

```shell
# 我们使用刚才已经创建好的容器进行创建
[root@ip-10-0-1-192 home]# docker images
REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
test01/centos   latest    90bc0d9dad3f   3 years ago   231MB
[root@ip-10-0-1-192 home]# 
[root@ip-10-0-1-192 home]# docker run -it --name test01 test01/centos 
[root@6a4ae4f63ea5 /]# ls -l
total 16
lrwxrwxrwx.   1 root root     7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   360 Nov 29 00:48 dev
......
drwxr-xr-x.   2 root root     6 Nov 29 00:48 volume01
drwxr-xr-x.   2 root root     6 Nov 29 00:48 volume02

# 创建第二个容器,ctrl+p+q
# 查看刚才创建的test01是否存在
[root@6a4ae4f63ea5 /]# [root@ip-10-0-1-192 home]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS     NAMES
6a4ae4f63ea5   test01/centos   "/bin/sh -c /bin/bash"   3 minutes ago   Up 3 minutes     

#启动第二个容器，并同时挂在test01的数据卷
[root@ip-10-0-1-192 home]# docker run -it --name test02 --volumes-from test01 test01/centos
[root@2e8b932df620 /]# ls -l
total 16
lrwxrwxrwx.   1 root root     7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   360 Nov 29 00:54 dev
drwxr-xr-x.   1 root root    66 Nov 29 00:54 etc
......
drwxr-xr-x.   2 root root     6 Nov 29 00:48 volume01
drwxr-xr-x.   2 root root     6 Nov 29 00:48 volume02

# 在目录volume01中创建一个文件
[root@2e8b932df620 /]# cd volume01
[root@2e8b932df620 volume01]# vi test02.txt

```



> 打开一个新的窗口，进入test01容器的volume目录，查看文件是否同步成功

```shell
# 使用 docker attach 容器ID 进入正在运行的容器
[root@ip-10-0-1-192 home]# docker attach 6a4ae4f63ea5 
[root@6a4ae4f63ea5 /]# cd volume01
[root@6a4ae4f63ea5 volume01]# ls 
test02.txt
[root@6a4ae4f63ea5 volume01]# cat test02.txt 
165725367887

```



> 问题：如果删除test01容器test02挂载的卷会不会消失

```shell
# 删除容器test01
[root@ip-10-0-1-192 home]# docker ps 
CONTAINER ID   IMAGE           COMMAND                  ....   NAMES
2e8b932df620   test01/centos   "/bin/sh -c /bin/bash"   ....   test02
6a4ae4f63ea5   test01/centos   "/bin/sh -c /bin/bash"   ....   test01

[root@ip-10-0-1-192 home]# docker rm -f 6a4ae4f63ea5 
6a4ae4f63ea5

# 进入容器test02查看
[root@2e8b932df620 volume01]# ls -l
total 4
-rw-r--r--. 1 root root 13 Nov 29 00:57 test02.txt
# 文件还是存在
```

 

#### 结论：

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器为止

但是一旦你持久化到本地，这个时候，本地的数据视不会删除的





## Dockerfile

大家想想，Nginx，tomcat，mysql 这些镜像都是哪里来的？官方能写，我们不能写吗？

我们要研究自己如何做一个镜像，而且我们写的微服务项目以及springboot打包上云部署，Docker就是 最方便的。

微服务打包成镜像，任何装了Docker的地方，都可以下载使用，极其的方便。

流程：开发应用=>DockerFile=>打包为镜像=>上传到仓库（私有仓库，公有仓库）=> 下载镜像 => 启动 运行。



什么是dockerfile

dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。 

构建步骤：

1. 编写dockerfile文件
2. docker build 构建镜像
3. docker run



### Dockerfile的命令

```shell
FROM		# 使用的基础镜像，当前镜像是基于那个镜像的
MAINTAINER  # 维护者的姓名+邮箱地址
RUN			# 容器构建时候需要运行的命令
EXPOSE		# 当前容器对外保留的端口
WORKDIR		# 指定在容器创建后，终端默认登录的进来工作目录
ENV			# 用来在构建镜像过程中设置环境变量
ADD			# 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和TAR
COPY		# 类似ADD，拷贝文件和目录到镜像中
VOLUME		# 容器数据卷，用于数据保存持久化工作
CMD 		# 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最
后一个生效！
ENTRYPOINT 	# 指定一个容器启动时要运行的命令！和CMD一样
ONBUILD		# 当构建一个被继承的DockerFile时运行命令，父镜像在被子镜像继承后，父镜像的ONBUILD被触发
```

![image-20241202084150623](https://github.com/user-attachments/assets/6a080056-10e8-4fa6-a1aa-88ac9ec6bd6d)




### 实战测试

> 创建一个自己的centos

```shell
# 编写dockerfile
[root@ip-10-0-1-192 dockerfile-test]# cat mydockerfile-centos 
FROM centos 
MAINTAINER owen_w<gonekooing@gmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-------end--------"
CMD /bin/bash

# 构建镜像
# docker build -f dockerfile地址 -t 标签名称 .
[root@ip-10-0-1-192 dockerfile-test]# docker build -f mydockerfile-centos -t mycentos:1.0 .
[+] Building 0.4s (6/6) FINISHED                                                     
```



### CMD和ENTRYPOINT的区别

```shell
CMD 		# 指定一个容器启动时要运行的命令，dockerFile中可以有多个CMD指令，但只有最
后一个生效！
ENTRYPOINT 	# 指定一个容器启动时要运行的命令！和CMD一样
```



> CMD测试

```shell
# 首先我们构建一个最基础的镜像，只运行一个CMD命令
[root@ip-10-0-1-192 dockerfile-test]# vi dockerfile-test-cmd
[root@ip-10-0-1-192 dockerfile-test]# cat dockerfile-test-cmd 
FROM centos 
CMD ["ls", "-a"]

[root@ip-10-0-1-192 dockerfile-test]# docker build -f dockerfile-test-cmd -t cmd-test .
[+] Building 0.1s (5/5) FINISHED                                                                                          docker:default
 => [internal] load build definition from dockerfile-test-cmd                                                                       0.0s
 => => transferring dockerfile: 138B                                                                                                0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                                    0.0s
 => [internal] load .dockerignore                                                                                                   0.0s
 => => transferring context: 2B                                                                                                     0.0s
 => CACHED [1/1] FROM docker.io/library/centos:latest                                                                               0.0s
 => exporting to image                                                                                                              0.0s
 => => exporting layers                                                                                                             0.0s
 => => writing image sha256:7d202bdf002be182b794b7f2b4c90c4fe3560c3ac4f8cebc27f1c8a94ab10a9a                                        0.0s
 => => naming to docker.io/library/cmd-test                   
 
 # 启动容器进行测试
 [root@ip-10-0-1-192 dockerfile-test]# docker images -a
REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
cmd-test        latest    7d202bdf002b   3 years ago   231MB
.......
# 启动容器后能正常显示
[root@ip-10-0-1-192 dockerfile-test]# docker run -it 7d202bdf002b 
.  ..  .dockerenv  bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 尝试在启动容器的时候在后续追加一个命令，
[root@ip-10-0-1-192 dockerfile-test]# docker run -it 7d202bdf002b -l
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-l": executable file not found in $PATH: unknown.
```



> ENTRYPOINT的测试

```shell
# 构建容器镜像
[root@ip-10-0-1-192 dockerfile-test]# vim dockerfile-entrypoint-test
[root@ip-10-0-1-192 dockerfile-test]# cat dockerfile-entrypoint-test 
FROM centos
ENTRYPOINT ["ls", "-a"]

[root@ip-10-0-1-192 dockerfile-test]# docker build -f dockerfile-entrypoint-test -t entrypoint-test .
[+] Building 0.1s (5/5) FINISHED                                                                                          docker:default
 => [internal] load build definition from dockerfile-entrypoint-test                                                                0.0s
 => => transferring dockerfile: 150B                                                                                                0.0s
 => [internal] load metadata for docker.io/library/centos:latest                                                                    0.0s
 => [internal] load .dockerignore                                                                                                   0.0s

[root@ip-10-0-1-192 dockerfile-test]# 
[root@ip-10-0-1-192 dockerfile-test]# docker images -a
REPOSITORY        TAG       IMAGE ID       CREATED       SIZE
cmd-test          latest    7d202bdf002b   3 years ago   231MB
entrypoint-test   latest    b325f5b97233   3 years ago   231MB
[root@ip-10-0-1-192 dockerfile-test]# 
[root@ip-10-0-1-192 dockerfile-test]# docker run b325f5b97233
.
..
.dockerenv
bin
dev
etc
home
........
[root@ip-10-0-1-192 dockerfile-test]# docker run b325f5b97233 -l
total 16
drwxr-xr-x.   1 root root     6 Dec  2 06:26 .
drwxr-xr-x.   1 root root     6 Dec  2 06:26 ..
-rwxr-xr-x.   1 root root     0 Dec  2 06:26 .dockerenv
lrwxrwxrwx.   1 root root     7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x.   5 root root   340 Dec  2 06:26 dev
drwxr-xr-x.   1 root root    66 Dec  2 06:26 etc
drwxr-xr-x.   2 root root     6 Nov  3  2020 home
.....

```



### 实战演练-独角兽镜像

```dockerfile
# 基于一个合适的镜像
FROM public.ecr.aws/amazonlinux/amazonlinux:latest

# 设置工作目录
RUN yum update -y && \
    mkdir /app
WORKDIR /app

# 将本地的文件复制到容器中
COPY server.ini /app/server.ini
COPY ws-ec2-root /app/ws-ec2-root

# 授权可执行权限
RUN chmod +x /app/ws-ec2-root
RUN chmod +x /app/server.ini

# 设置环境变量
ENV AWS_DEFAULT_REGION=us-east-1

# 暴露端口 80
EXPOSE 80

# 设置容器启动时执行的命令
CMD ["./ws-ec2-root"]
```

## Docker网络

### 理解docker0

准备工作：清空所有的容器，清空所有的镜像

Docker的网络也是十分重要的一个点，希望大家可以认真理解！



> 我们先来做个测试

查看本地的IP `ip addr`

![image-20241209093646654](https://github.com/user-attachments/assets/8f12a442-0763-40ff-a462-8210b7a03e1d)


这里我们分析可得，有三个网络：

```shell
lo 		127.0.0.1		# 本机的回环地址
enX0	10.0.1.206		# aws上的私有IP
docker0	172.17.0.1		# docker网桥
# 问题：docker是如何处理网络访问的？
```

我们可以创建两个容器测试他们能不能相互ping通或者与主机ping通

```shell
# 下载镜像
[root@ip-10-0-1-206 ec2-user]# docker pull centos

# 启动容器并查看test01-centos的IP地址
[root@ip-10-0-1-206 ec2-user]# docker run -it --name test01-centos centos ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
4: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
# docker会给每个容器都分配一个ip

# 思考，我们的linux主机是否能ping通容器 test01-centos 
[root@ip-10-0-1-206 ec2-user]# ping -c 4 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
From 172.17.0.1 icmp_seq=1 Destination Host Unreachable
```



> 我们发现主机不能直接ping通容器内的地址这是为什么呢？

### 问题分析：

1. **容器网络**： 容器使用 Docker 默认的 `bridge` 网络模式。该模式通过创建一个虚拟网桥（`docker0`）来连接宿主机和容器。在这种模式下，容器的 IP 地址（如 `172.17.0.2`）与宿主机的网络不同，因此容器与宿主机在网络上是隔离的。
2. **宿主机与容器之间的连接**： 宿主机的 `docker0` 接口拥有 IP 地址 `172.17.0.1`，这是宿主机和容器网络之间的网关。容器的 IP 地址 `172.17.0.2` 位于 `docker0` 网桥的子网内。宿主机不直接能够 ping 容器的 IP 地址。

### 解决方法：



#### 1. **使用 Host 网络模式（如果需要直接访问）**

如果你希望容器和宿主机共享网络（直接访问容器的 IP 地址），可以使用 `host` 网络模式：

```bash
docker run -it --name test01-centos --network host centos ip addr
```

这将使容器与宿主机共享相同的网络堆栈，可以直接使用宿主机的 IP 地址来访问容器。

#### 2. **连接容器与宿主机的网络（使用 `--add-host`）**

如果你有多个容器需要与宿主机进行通信，可以通过修改容器的 `/etc/hosts` 来实现更灵活的网络配置：

```bash
docker run -it --name test01-centos --add-host=host.docker.internal:host-gateway centos
```

这将使容器能够通过 `host.docker.internal` 地址访问宿主机。



### 小结

Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据 Docker网桥的网段分配给容器一个IP地址，称为Container-IP，同时Docker网桥是每个容器的默认网 关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够通过容器的Container-IP直接 通信

![image-20241209100529432](https://github.com/user-attachments/assets/67b320d9-8f68-4028-be6a-4afedba1aa3f)


Docker容器网络就很好的利用了Linux虚拟网络技术，在本地主机和容器内分别创建一个虚拟接口，并 让他们彼此联通（这样一对接口叫veth pair）；

Docker中的网络接口默认都是虚拟的接口。虚拟接口的优势就是转发效率极高（因为Linux是在内核中 进行数据的复制来实现虚拟接口之间的数据转发，无需通过外部的网络设备交换），对于本地系统和容 器系统来说，虚拟接口跟一个正常的以太网卡相比并没有区别，只是他的速度快很多。



### docker --link

思考一个场景，我们编写一个微服务，数据库连接地址原来是使用ip的，如果ip变化就不行了，那我们 能不能使用服务名访问呢？

jdbc:mysql://mysql:3306，这样的话哪怕mysql重启，我们也不需要修改配置了！docker提供了 --link  的操作！

```shell
[root@ip-10-0-1-206 ec2-user]# docker run -it -d -P --name test03 --link test01 centos 
```

思考，这个原理是什么呢？我们进入tomcat03中查看下host配置文件

```shell
[root@ip-10-0-1-206 ec2-user]# docker exec -it test03 cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      test01 f1c722b1a40c
172.17.0.4      8bf58c52c6c5

# 所以这里其实就是配置了一个 hosts 地址而已！
# 原因：--link的时候，直接把需要link的主机的域名和ip直接配置到了hosts文件中了。
```



### 容器的自定义网络

>  查看docker的所有网络

![image-20241209130635162](https://github.com/user-attachments/assets/d76f2eca-fdf5-449b-a1ce-7c16562ca004)


|    网络模式    |           配置           |                             说明                             |
| :------------: | :----------------------: | :----------------------------------------------------------: |
|   bridge模式   |       --net=bridge       |      默认值，在Docker网桥docker0上为容器创建新的网络 栈      |
|    none模式    |        --net=none        |          不配置网络，用户可以稍后进入容器，自行配置          |
| container 模式 | \- net=container:name/id | 容器和另外一个容器共享Network namespace。 kubernetes中的pod就是多个容器共享一个Network namespace。 |
|    host模式    |        --net=host        |              容器和宿主机共享Network namespace               |

![image-20241209133917080](https://github.com/user-attachments/assets/9adb6b3b-5da6-4cd1-8d5f-fbb2665d0c86)


> 尝试自己创建容器网卡

1、删除原来的所有容器保持环境的干净

![image-20241209133213944](https://github.com/user-attachments/assets/39ac18b0-0377-4c21-a290-c97f4f946b41).png)



2、接下来我们来创建容器，但是我们知道默认创建的容器都是docker0网卡的

```shell
# 默认我们不配置网络，也就相当于默认值 --net bridge  使用的docker0
 docker run -d -P --name tomcat01 --net bridge tomcat
# docker0网络的特点
 1.它是默认的
 2.域名访问不通
 3.--link 域名通了，但是删了又不行
```



3、我们可以自己创建网卡并让容器启动的时候使用自定义网络

![image-20241209134057104](https://github.com/user-attachments/assets/da473249-5bdf-4d29-b111-fc93c7e3d61e)


```shell
# 自定义docker网络
[root@ip-10-0-1-206 ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
01cd95f72f46092d3ce4d76a7f33f8f17c3e1eb9620bd481ac9fa3b2d1d80f2b

# 确认是否创建成功
[root@ip-10-0-1-206 ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
426aa83ae2e8   bridge    bridge    local
d0d56566f436   host      host      local
01cd95f72f46   mynet     bridge    local
a1162d4c73e9   none      null      local

# 查看mynet网卡的详细信息
[root@ip-10-0-1-206 ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "01cd95f72f46092d3ce4d76a7f33f8f17c3e1eb9620bd481ac9fa3b2d1d80f2b",
        "Created": "2024-12-09T05:42:48.570384851Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```



> 启动两个容器并使用自己创建的网卡

```shell
[root@ip-10-0-1-206 ~]# docker run -d -P --name centos01 --net mynet centos ping 192.168.1.1
[root@ip-10-0-1-206 ~]# docker run -d -P --name centos02 --net mynet centos ping 192.168.1.1

[root@ip-10-0-1-206 ~]# docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS     NAMES
222522d82c9f   centos    "ping 192.168.1.1"   3 seconds ago    Up 2 seconds              centos02
db5b7fad5c89   centos    "ping 192.168.1.1"   21 seconds ago   Up 20 seconds             centos01

[root@ip-10-0-1-206 ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "01cd95f72f46092d3ce4d76a7f33f8f17c3e1eb9620bd481ac9fa3b2d1d80f2b",
        "Created": "2024-12-09T05:42:48.570384851Z",
		..........

        "Containers": {
            "222522d82c9fea3a17cb4971c9cd586cbf25729018fce16a3cfad98ff18510f4": {
                "Name": "centos02",
                "EndpointID": "85fcb51f94534eded3c22e2526b9e7c67d32fc2521b9622698a5a7e90c7606d5",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "db5b7fad5c892e3ce89439dc85365d7a40abdb688ff91466c8d2116d65b09945": {
                "Name": "centos01",
                "EndpointID": "1c94c710c588d280d4c5fa1f30db9f2edae22adea5cc50da7694050e8db49e9e",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

```



> 接下来，我们来测试ping容器名和ip试试，都可以ping通

```shell
[root@ip-10-0-1-206 ~]# docker exec -it centos01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=127 time=0.060 ms
--- 192.168.0.3 ping statistics ---

[root@ip-10-0-1-206 ~]# docker exec -it centos01 ping centos02
PING centos02 (192.168.0.3) 56(84) bytes of data.
64 bytes from centos02.mynet (192.168.0.3): icmp_seq=1 ttl=127 time=0.039 ms
--- centos02 ping statistics ---

# 测试发现可以通过IP和容器名去ping对方
```
