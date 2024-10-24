# **使用 Transit Gateway 进行多播**

![image-20241021075932340](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021075932340.png)

### 任务一：测试多播发现不起作用

你的任务是：

- [使用Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-sessions-start.html#start-ec2-console)打开与每个 EC2 实例的会话。在每个会话上使用命令`omping`检查多播网络流量。从 获取命令`Output Properties`，它被标识为`MulticastTestCommand`

您会注意到`omping`只显示单播线路。没有显示多播。多播将 100% 丢失。这意味着多播在您的 VPC 上不起作用。

让它运行几秒钟。过一会儿按 CTRL+C 退出。
按 CTRL+C 时，它会显示所有其他 IP 的统计信息。

在下面的例子中，您可以看到 CTRL+C`^C`和统计信息为最后 4 行。
请注意，多播将有 100% 的损失。

**例子：**

```shell
[root@ip-10-0-0-92 ~]# omping -m 224.10.10.10 10.0.0.92 10.0.1.111 10.0.2.79
10.0.1.111 : waiting for response msg
10.0.2.79  : waiting for response msg
10.0.1.111 : waiting for response msg
10.0.2.79  : waiting for response msg
10.0.1.111 : joined (S,G) = (*, 224.10.10.10), pinging
10.0.1.111 :   unicast, seq=1, size=69 bytes, dist=0, time=2.431ms
10.0.2.79  : waiting for response msg
10.0.2.79  : joined (S,G) = (*, 224.10.10.10), pinging
10.0.2.79  :   unicast, seq=1, size=69 bytes, dist=0, time=0.865ms
10.0.1.111 :   unicast, seq=2, size=69 bytes, dist=0, time=2.569ms
10.0.2.79  :   unicast, seq=2, size=69 bytes, dist=0, time=0.954ms
10.0.1.111 :   unicast, seq=3, size=69 bytes, dist=0, time=2.466ms
^C
10.0.1.111 :   unicast, xmt/rcv/%loss = 15/15/0%, min/avg/max/std-dev = 2.431/2.532/3.231/0.198
10.0.1.111 : multicast, xmt/rcv/%loss = 15/0/100%, min/avg/max/std-dev = 0.000/0.000/0.000/0.000
10.0.2.79  :   unicast, xmt/rcv/%loss = 14/14/0%, min/avg/max/std-dev = 0.865/0.948/1.055/0.041
10.0.2.79  : multicast, xmt/rcv/%loss = 14/0/100%, min/avg/max/std-dev = 0.000/0.000/0.000/0.000
```





### 任务二：中转网关

![image-20241021083537739](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021083537739.png)

![image-20241021083545855](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021083545855.png)

#### 解答：

#### 中转网关

进入VPC控制台页面，按照以下步骤操作：

- `Transit Gateways`在左侧菜单上选择
- 选择`Create transit gateway`
- 在`Name tag`现场增加`Jam`价值
- 在`Description`现场增加`Jam`价值
- 选择`Multicast support`以确保其将被启用
- 选择`Create transit gateway`
- 等待状态`Available`

#### 中转网关附件

进入VPC控制台页面，按照以下步骤操作：

- `Transit Gateway Attachments`在左侧菜单上选择

- 选择`Create transit gateway attachment`

- 在`Name tag`现场增加`Jam`价值

- 在`Transit gateway ID`字段上，选择之前创建的一个，名为`Jam`

- 在`VPC ID`字段中，选择名为`Jam`

- 确保

  ```
  Subnet IDs
  ```

  仅选择前 3 个可用区域

  - 在第一个可用区上，选择名为`Subnet1`
  - 在第二个可用区上，选择名为`Subnet2`
  - 在第三个可用区上，选择名为`Subnet3`

- 选择`Create transit gateway attachment`

- 等待状态`Available`





### 任务三：创建多播域

![image-20241021084432152](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021084432152.png)



![image-20241021084442504](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021084442504.png)



**创建 Transit Gateway 多播域**：

- 进入 VPC 控制台。
- 在左侧菜单中选择“Transit Gateway Multicast”。
- 选择“创建中转网关多播域”。
- 在名称标签字段中输入“Jam”。
- 选择名为“Jam”的中转网关ID。
- 选择“IGMPv2 support”以确保实现。
- 选择“创建中转网关组播域”，并等待状态变为“可用”。

**中转网关多播域关联**：

- 进入 VPC 控制台。
- 在左侧菜单中选择“Transit Gateway Multicast”。
- 选择“tgw-mcast-domain-123abc”（名为“Jam”的那个）。
- 选择“协会”。
- 选择“创建关联”。
- 选择名为“Jam”的附件。
- 选择名为“Subnet1”、“Subnet2”和“Subnet3”的子网。
- 选择“创建关联”，并确保所有三个关联均处于等待状态。

![image-20241021084659839](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021084659839.png)





任务四：创建安全组

![image-20241021084920795](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021084920795.png)

![image-20241021084943312](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021084943312.png)

![image-20241021085743277](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021085743277.png)

### 任务四：测试多播

```shell
sudo sysctl -w net.ipv4.conf.eth0.force_igmp_version=2
```

#### 入门：

Transit Gateway 支持 IGMP 版本 2，而不支持更高版本 3。因此，任何预期接收多播流量的系统都必须配置为支持 IGMPv2。对于 Linux，就像本 Jam 挑战赛的情况一样，这可以通过设置相应的内核参数来完成。以下命令显示了如何操作：

```shell
sudo sysctl -w net.ipv4.conf.eth0.force_igmp_version=2
```

您可以使用以下命令检查其配置的版本：

```shell
[root@ip-10-0-0-92 ~]# cat /proc/net/igmp
Idx     Device    : Count Querier       Group    Users Timer    Reporter
1       lo        :     1      V3
                                010000E0     1 0:00000000               0
2       eth0      :     1      V2
                                010000E0     1 0:00000000               0
```

------

##### ❌👮注意！

以这种方式更改内核参数不会使其持久，因此每次启动时都必须运行此命令或类似命令。

------

你的任务是：

- 配置所有 3 个实例以支持 IGMPv2
- 同时在所有 3 个实例上并行运行`omping`命令。这需要一些时间，请耐心等待！`MulticastTestCommand`

继续运行。你应该看到它像下面这样运行：

```shell
10.0.0.92 :   unicast, seq=21, size=69 bytes, dist=0, time=2.106ms
10.0.0.92 : multicast, seq=21, size=69 bytes, dist=0, time=2.113ms
10.0.2.79 :   unicast, seq=20, size=69 bytes, dist=0, time=1.330ms
10.0.2.79 : multicast, seq=20, size=69 bytes, dist=0, time=1.715ms
10.0.0.92 :   unicast, seq=22, size=69 bytes, dist=0, time=2.117ms
10.0.0.92 : multicast, seq=22, size=69 bytes, dist=0, time=2.125ms
10.0.2.79 :   unicast, seq=21, size=69 bytes, dist=0, time=1.274ms
10.0.2.79 : multicast, seq=21, size=69 bytes, dist=0, time=1.357ms
```

如果您没有看到带有 的行`multicast`，如下面的例子所示，那就是错误的。

```shell
 10.0.2.79 :   unicast, seq=1, size=69 bytes, dist=0, time=0.865ms
 10.0.0.92 :   unicast, seq=2, size=69 bytes, dist=0, time=2.569ms
 10.0.2.79 :   unicast, seq=2, size=69 bytes, dist=0, time=0.954ms
 10.0.0.92 :   unicast, seq=3, size=69 bytes, dist=0, time=2.466ms
10.0.0.92 : waiting for response msg
10.0.2.79 : waiting for response msg
10.0.0.92 : waiting for response msg
10.0.2.79 : waiting for response msg
```

另外，请检查您的传输网关多播域组。
您应该在组 IP 地址`224.0.0.1`和上看到来自 3 个实例的 ENI `224.10.10.10`。

请参阅下面的示例

![image-20241021090626593](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021090626593.png)

![image-20241021090648185](C:\Users\Owen\AppData\Roaming\Typora\typora-user-images\image-20241021090648185.png)