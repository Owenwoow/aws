## 题目：

#### 背景

AnyCompany 网络上的一台受感染主机会定期向另一台主机发送信标。信标中包含一条消息，安全团队可利用该消息来识别其他受感染主机。

#### 你的任务

使用 AnyCompany`JamMonitorStation`捕获受感染者`JamInstanceA`向目标发送的信标`JamInstanceB`。要完成此挑战，您需要：

- 正确配置流量镜像，以便`JamMonitorStation`可以捕获由`JamInstanceA`
- 登录到`JamMonitorStation`网站`System Manager's Session Manager`来捕获流量。

#### 入门

要开始，请单击`Start Challenge`，然后单击`Open AWS Console`，并导航到`Systems Manager Session Manager`登录监控站。

#### 存货

- `JamMonitorStation`
- `JamInstanceA`
- `JamInstanceA`

#### 您应该使用的服务

- `EC2`
- `VPC`
- `SSM`


#### 任务验证

要得到答案，您需要识别`JamInstanceA`和之间的网络流量中隐藏的消息`JamInstanceB`，然后将隐藏的消息放入 JAM 答案字段。



## 原理&解答

### 1. **流量镜像的原理**

流量镜像（Traffic Mirroring）允许你将特定的网络流量复制到指定的目标网络接口，以便进行分析和监控。在 AWS 中，流量镜像可以帮助你监控流经虚拟私有云（VPC）中的实例的网络流量。

- **镜像源（Mirror Source）**：指定你想要复制流量的网络接口，即你想监控流量的实例。我们在这里选择了 **JamInstanceA**，因为它是受感染的主机，需要监控其流量。
- **镜像目标（Mirror Target）**：流量将被复制到这个目标接口，也就是监控实例。在这个案例中，**JamMonitorStation** 是目标，我们需要将从 JamInstanceA 收集的流量发送到这个监控站以进行分析。
- **VXLAN 封装**：流量镜像使用 VXLAN（Virtual Extensible LAN）协议将流量封装，并通过网络传输到镜像目标。VXLAN 是一种隧道协议，它允许你将流量从一个网络设备发送到另一个设备，并在网络传输过程中封装数据帧。

### 2. **VXLAN 与 UDP 端口 4789**

流量镜像通过 VXLAN 封装流量，这意味着它会在 **UDP 端口 4789** 上发送数据包。这个端口专门用于 VXLAN 隧道流量，因此我们必须确保 **JamMonitorStation** 上的安全组允许通过这个端口的入站流量。

为了让 JamMonitorStation 接收 VXLAN 流量，我们需要修改它的安全组，允许来自任何地方（`0.0.0.0/0`）的 UDP 4789 端口入站流量。


![image-20241017123815525](https://github.com/user-attachments/assets/4bc3e877-d8bb-4839-872d-47cc87f055e6)



### 3. **配置流量镜像**

#### 3.1 **镜像源和镜像目标**

流量镜像的核心是将 **JamInstanceA** 的流量复制到 **JamMonitorStation**。在 AWS 控制台中，我们通过以下几个步骤实现这一点：

- **镜像源**：我们选择 **JamInstanceA** 的网络接口，因为我们希望监控从这台受感染主机发送的流量。这个接口 ID 可以在 EC2 的网络属性中找到。

![image-20241017125848618](https://github.com/user-attachments/assets/ca802ca8-e802-429a-ae24-422ee6fb2703)


- 创建镜像源

(https://github.com/user-attachments/assets/31b42335-777a-45c7-9ee4-5939a1f4261a)


- **镜像目标**：这个目标是 **JamMonitorStation** 的网络接口，它将接收到来自 JamInstanceA 的流量。创建一个新的镜像目标时，我们必须指定 JamMonitorStation 的接口 ID。

  镜像筛选条件：

![image-20241017141436268](https://github.com/user-attachments/assets/19b9d79d-b7aa-4c65-a2c0-e5ad376499db)


![image-20241017141530193](https://github.com/user-attachments/assets/31e47bed-d2a0-47ac-98ba-3d67fc2b2940)


### 4. **流量捕获与分析**

#### 4.1 **VXLAN 解封**

VXLAN 封装意味着流量被包装在一个额外的网络层中。当我们在 **JamMonitorStation** 上捕获流量时，首先需要解封这些数据包，这样才能查看其中的内容。这就是为什么我们需要在监控站上创建一个 **VXLAN 接口**。

使用以下命令来创建并启用 VXLAN 接口：

```
bashCopy codesudo ip link add vxlan0 type vxlan id 100 dev eth0 local 10.0.1.11 dstport 4789
sudo ip link set vxlan0 up
```

这些命令会：

- 创建一个新的网络接口 `vxlan0`，它会监听 VXLAN ID `100`。
- 设置接口为活动状态，使其可以接收并解封封装的 VXLAN 流量。

#### 4.2 **捕获 ICMP 流量**

**ICMP（Internet Control Message Protocol）** 是网络中用于发送错误消息或进行简单通信的协议。在这个挑战中，恶意的 JamInstanceA 会通过 ICMP 发送消息给 JamInstanceB，因此我们使用 **tcpdump** 工具来捕获 ICMP 流量：

```
bash


Copy code
sudo tcpdump -lnvXi vxlan0 icmp
```

- **tcpdump** 是一个命令行工具，用于捕获网络流量。
- `-lnvX` 参数表示将以人类可读的方式输出详细信息。
- `vxlan0` 是我们创建的接口，它负责解封流量并监听 VXLAN 封装的流量。
- **ICMP** 是我们关心的协议，因为恶意流量通过 ICMP 发送。

通过这个命令，我们可以查看 **JamInstanceA 发送给 JamInstanceB 的 ICMP ECHO 请求**，其中隐藏了一条消息。这条消息通常被嵌入到 ICMP 请求的有效载荷（payload）字段中。



### **5.捕获隐藏的消息并提交答案**

#### 数据包解析

首先，我们看到这部分是完整的 ICMP echo reply 数据包：

```
yamlCopy code06:10:12.835327 IP (tos 0x0, ttl 255, id 52663, offset 0, flags [none], proto ICMP (1), length 56)
    10.0.3.10 > 10.0.2.10: ICMP echo reply, id 2335, seq 18356, length 36
        0x0000:  4500 0038 cdb7 0000 ff01 d4f9 0a00 030a  E..8............
        0x0010:  0a00 020a 0000 6c13 091f 47b4 44aa 1067  ......l...G.D..g
        0x0020:  0000 0000 22be 0c00 0000 0000 6a34 6d63  ....".......j4mc
        0x0030:  306d 506c 3374 3364                      0mPl3t3d
```

我们可以把它分成几个部分：

1. **IP头部部分（IP Header, `0x0000` - `0x001F`）**：

   ```
   yamlCopy code0x0000:  4500 0038 cdb7 0000 ff01 d4f9 0a00 030a  E..8............
   0x0010:  0a00 020a 0000 6c13 091f 47b4 44aa 1067  ......l...G.D..g
   ```

   这部分是 IP 头部信息，包括源地址 (`10.0.3.10`)、目的地址 (`10.0.2.10`)、协议 (ICMP, 协议号 1)，以及其他 IP 包的元数据。这部分一般是基础网络层的信息，不包含隐藏消息。

2. **ICMP 数据部分（ICMP Data, `0x0020` - `0x003F`）**：

   ```
   yamlCopy code0x0020:  0000 0000 22be 0c00 0000 0000 6a34 6d63  ....".......j4mc
   0x0030:  306d 506c 3374 3364                      0mPl3t3d
   ```

   这部分包含了 ICMP 协议的实际数据。在这里我们看到了一段以 `j4mc` 开头的内容，它是隐藏的信标消息。

### 如何识别出隐藏消息？

- **ICMP 的正常数据结构**：ICMP 数据包通常包含一些 ID、序列号等控制信息，但在 "payload" (有效载荷) 中是可以携带自定义数据的。在这个包中，从 `0x0020` 开始的部分是有效载荷字段，通常是自定义内容。在这里，我们看到的就是这个信标信息。

- **有效载荷中不规则的可读内容**：当我们逐字节解析这个包时，发现 `0x0020` 之后的字节序列 `6a34 6d63 306d 506c 3374 3364` 被解释为 ASCII 字符时就是：

  ```
  Copy code
  j4mc0mPl3t3d
  ```

  这段文字非常清晰地呈现了有效载荷中的隐藏消息，它就是 "j4mc0mPl3t3d"。

### 总结：

在 ICMP 数据包的结构中，有一个部分被用于携带数据。在这种情况下，您要分析的隐藏消息恰好被嵌入在 ICMP 数据包的有效载荷（payload）中，而不是传统的控制信息。这也是为什么通过 `tcpdump` 命令查看详细的 ICMP 数据包内容后，能够找到信标信息。

您最终识别到的有效载荷（`j4mc0mPl3t3d`）是这次捕获任务中的隐藏消息