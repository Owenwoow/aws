# systemd 实现服务自启动

server.root服务

##### 介绍：

​	*如果要让任务开机自启动，需将对应的Unit文件存放于/etc/systemd/system下。本文以Service Unit为例，但也支持让path Unit、timer Unit等类型的任务开机自启动。*



##### 配置文件:

------

```shell
[Unit]
Description=root Server Service
After=network.target

[Service]
User=root
ExecStart=/root/server/server -config /root/server/server.ini
WorkingDirectory=/root/server
Restart=on-failure
RestartSec=3
LimitNOFILE=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=server

[Install]
WantedBy=multi-user.target



```



------

ExecStart=后面需要添加启动命令脚本如

User=halo 用户名改为在使用的角色

**将以上内容写入/lib/systemd/system/halo.service，可以使用vim编辑器写入。注意，这一步需要root权限。**



##### 步骤如下：

##### 1.将配置文件写入/lib/systemd/system/server.service

```
vi /lib/systemd/system/server.service
```



##### 2.重新加载systemd配置文件：

```
systemctl daemon-reload
```



##### 3.启动server服务：

```
systemctl start server
```



4.查看server服务状态：

```
systemctl status server
```



##### 5.开机自启动server

```
systemctl enable serverer 
```
