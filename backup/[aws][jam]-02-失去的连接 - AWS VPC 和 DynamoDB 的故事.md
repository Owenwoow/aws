# **失去的连接 - AWS VPC 和 DynamoDB 的故事**

### 概述

曾几何时，在亚马逊网络服务的云王国中，存在着一个强大的虚拟私有云 (VPC)，由一个技术娴熟的无服务器战士 AWS Lambda 守护。Lambda 以惊人的速度和效率执行任务而闻名，这要归功于它能够利用无服务器计算的魔力。Lambda 最重要的任务之一是连接到神秘的数据库 Amazon DynamoDB，其中需要存储有价值的信息。AWS Lambda 位于 VPC 的安全墙内，可以安然无恙地生存，免受外部威胁。然而，在一个决定性的日子里，出现了一个不祥的问题。每当 AWS Lambda 尝试访问 Amazon DynamoDB 时，它最终都会失败。云之国传奇被要求调查这场灾难的原因。他们怀疑可能是恶意的网络配置错误在作怪，导致 VPC 的大门始终对 Amazon DynamoDB 紧闭。他们中最聪明的人决定踏上寻找问题根源的旅程





## 任务 1：神秘之门

### 背景

**名为PutItemLambdaFunction**的 lambda 函数旨在在**MyDynamoDBTable**中执行*PutItem*操作。但是，当从控制台执行时，它会遇到失败或超时。要解决此问题，建议手动触发 AWS Lambda 函数并验证它是否可以成功将数据写入**MyDynamoDBTable**。

#### 你的任务

您的目标是建立从**PutItemLambdaFunction**到**MyDynamoDBTable 的**连接并将项目写入其中。此任务涉及创建网络资源，以便从私有子网安全连接到 Amazon DynamoDB。MyDynamoDBTable位于 reinvent23-vpc 上**并****使用**reinvent23 **-private-subnet**。

#### 入门

- 点击**AWS 控制台**

- 导航到**VPC**服务，您可以在其中找到**reinvent23-vpc**、**reinvent23-private-subnet**和**reinvent-lambda-sg**

- 导航到

  Lambda

  服务，您可以在其中找到

  PutItemLambdaFunction

  。

  - 单击**PutItemLambdaFunction** ，这将打开 Lambda 控制台
  - 单击**“测试”**按钮，然后单击**“调用”**

#### 存货

您的 AWS 账户已配置以下资源：

- **AWS Lambda 函数：`PutItemLambdaFunction`**
- 
- **亚马逊 VPC：`reinvent23-vpc`**
- **Amazon VPC 子网：`reinvent23-private-subnet`*****\*Amazon VPC 路由表：`reinvent23-private-route-table`\**\**\*\*AWS 安全组：`reinvent-lambda-sg`\*\*\*\*\*\*亚马逊Dyanmodb：`MyDynamoDBTable`\*\*\*\*\****



### 线索：

创建连接dynamodb的终端节点（**创建时记得勾选静态节点**）





## 任务 2：提升守门人

### 背景

恭喜！Guardian 非常高兴您成功创建了适当的网络资源，现在允许建立与**MyDynamoDBTable的私有连接。但是，在****PutItemLambdaFunction**可以将项目写入**MyDynamoDBTable**之前，还有最后一个障碍需要解决。仍然缺少与**PutItemLambdaFunction**密切相关的东西

#### 你的任务

您的任务是识别并修复实体，以便**PutItemLambdaFunction**可以连接到正确的[[aws-managed-prefix-lists](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-aws-managed-prefix-lists.html)](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-aws-managed-prefix-lists.html)

**笔记！！**

- 您无法更改 Lambda 函数代码
- 您无法更改与 Lambda 函数关联的 IAM 角色

#### 入门

- 单击**打开 AWS 控制台**

- 导航到**VPC**服务，您可以在其中找到**reinvent23-vpc**、**reinvent23-private-subnet**和**reinvent-lambda-sg**

- 导航到

  Lambda

  服务，您可以在其中找到

  PutItemLambdaFunction

  。

  - 单击**PutItemLambdaFunction** ，这将打开 Lambda 控制台
  - 单击**“测试”**按钮，然后单击**“调用”**

#### 存货

您的 AWS 账户已配置以下资源：

- **AWS Lambda 函数：`PutItemLambdaFunction`**
- 
- **亚马逊 VPC：`reinvent23-vpc`**
- **Amazon VPC 子网：`reinvent23-private-subnet`*****\*AWS 安全组：`reinvent-lambda-sg`\**\**\*\*亚马逊Dyanmodb：`MyDynamoDBTable`\*\**\***

**任务验证**

***\*您需要执行\*\*PutItemLambdaFunction 。一旦\*\*\*\*MyDynamoDBTable\*\*有 Item（`item_1`）， 任务将自动验证。\****





### 线索 1: 使用 VPC 终端节点策略限制对 DynamoDB 表的访问


当 AWS Lambda Function 在 VPC 内运行时，它还会链接到一个安全组。该安全组必须配置适当的出站规则才能允许目标服务进行通信。托管前缀列表是一个或者多个 CIDR 块的集合，使用前缀列表可以简化安全组的设置和管理。





### 线索 2: 解决挑战的分步说明



要解决 TASK 2，请按照说明进行操作。

**更新安全组规则**
要更新 `reinvent-lambda-sg` 安全组规则，首先我们需要知道 DynamoDB 前缀列表 ID 是什么。

使用控制台查看 AWS 管理的前缀列表：

1. 通过 [https://console.aws.amazon.com/vpc/](https://console.aws.amazon.com/vpc/) 打开亚马逊 VPC 控制台。

现在让我们更新安全组规则：

1. 打开 Amazon EC2 控制台，网址为 [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)，在导航窗格中，选择 `reinvent-lambda-sg` 安全组。
2. 选择操作，编辑出站规则，更新出站流量的规则。
3. 单击 “添加规则”：
   - 类型：Custom TCP
   - 端口范围：443
   - 目标：Custom
4. 然后在下一个字段框中，选择你在上一步中记下的 Prefix list ID。
5. 选择 “保存规则”。

完成后，请前往 Lambda 控制台执行 `putItemLambdaFunction`。

---

如果你有其他问题或需要进一步的帮助，请告诉我。