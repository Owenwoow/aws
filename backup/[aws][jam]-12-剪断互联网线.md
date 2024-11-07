# **剪断互联网线！**

![image-20241020094227557](https://github.com/user-attachments/assets/de9ef56a-169d-4be3-89cc-e5aab7ff90c8)



## 任务 1：授予 Lambda 写访问权限！

![image-20241020094412642](https://github.com/user-attachments/assets/deeff23d-9ac5-45b8-932c-c8265c5dd956)


https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/reference_policies_examples_dynamodb_specific-table.html

Amazon DynamoDB：允许访问特定的表

![image-20241020095112298](https://github.com/user-attachments/assets/9842250f-5f08-4189-bb4a-f36a4829cc19)


```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListAndDescribe",
            "Effect": "Allow",
            "Action": [
                "dynamodb:List*",
                "dynamodb:DescribeReservedCapacity*",
                "dynamodb:DescribeLimits",
                "dynamodb:DescribeTimeToLive"
            ],
            "Resource": "*"
        },
        {
            "Sid": "SpecificTable",
            "Effect": "Allow",
            "Action": [
                "dynamodb:BatchGet*",
                "dynamodb:DescribeStream",
                "dynamodb:DescribeTable",
                "dynamodb:Get*",
                "dynamodb:Query",
                "dynamodb:Scan",
                "dynamodb:BatchWrite*",
                "dynamodb:CreateTable",
                "dynamodb:Delete*",
                "dynamodb:Update*",
                "dynamodb:PutItem"
            ],
            "Resource": "arn:aws:dynamodb:*:*:table/MyTable"
        }
    ]
}
```

但是题目中已经创建了iam用户

直接附加就行





## 任务 2：保护 Lambda！



![image-20241020095614802](https://github.com/user-attachments/assets/609b734a-2ecf-486c-8abb-ec9ed40fdd12)


#### 1.将vpc附加到lamdba

![image-20241020100631353](https://github.com/user-attachments/assets/4d4a40dc-d2d5-42b9-a0ce-bf461ded4ccb)


#### 2.创建dynamodb的终端节点

1. 在 AWS 控制台中，导航到 VPC 仪表板。
2. 在下`Virtual private cloud`，选择`Endpoints`
3. 选择`Create endpoint`
4. 创建端点：
   1. 服务类别：`AWS Services`
   2. 服务：`com.amazonaws.{YOUR-REGION}.dynamodb`
   3. 私有云平台：`InternetCordJamVPC`
   4. 路由表：与每个私有子网关联的路由表
   5. 保留其余默认设置并选择`Create endpoint`
5. 通过运行在任务 1 中创建的测试事件来验证您的 Lambda 是否具有访问权限。它应该打印所有当前项目并将新项目写入表中。

![Uploading image-20241020100754308.png…]()




## 任务3：锁定连接！

### 背景

恭喜，JAS Corporate 很高兴你的 AWS Lambda 与亚马逊 DynamoDB 的连接仅为私有连接！但是，作为安全工程师，您知道应该进一步加强 Lambda 的网络连接。

您需要确保您的 AWS Lambda 仅允许与 Amazon DynamoDB 进行通信，不允许其他任何通信，以符合最低权限原则。

### 你的任务

更新 Lambda 通信以仅允许访问亚马逊 DynamoDB。

### 库存

*已为本次挑战预先创建了以下资源：*

- Lambda 名为 InternetCordJamLambda
- 名为 InternetCordJamVpc 的 VPC
- 1 个名为 InternetCordJamSecurityGroup 的安全组

### 你应该使用的服务

- AWS Lambda
- 亚马逊 VPC

### 入门

- 确保 Lambda 只能通过网络配置与 Amazon DynamoDB 通信
- 请注意，亚马逊 DynamoDB 终端节点使用 HTTP/HTTPS 协议进行 API 连接

### 任务验证

- 找到解决方案后，任务将自动完成。
- 你可以随时点击挑战详情屏幕中的“查看我的进度”按钮来查看自己的进度。

### 任务成功必须符合的因素

- AWS Lambda 只能允许严格访问亚马逊 DynamoDB

---

这是关于如何完成挑战的分步演练。本指南将帮助您：

- 编辑您的安全组出站规则，仅允许使用 AWS 管理的前缀列表访问 Amazon DynamoDB

### 编辑安全组

1. 在 AWS 控制台中导航到 VPC 控制面板。
2. 在 Security 下，选择 Security groups
3. 选择名为 InternetCordJamSecurityGroup 的安全组
4. 在群组详细信息页面上，选择 Outbound rules。请注意，当前有一条允许所有出站流量的出站规则。我们将将其更改为仅允许访问 DynamoDB 的已知 IP 地址，该地址由 AWS 管理的前缀列表委托。
5. 打开亚马逊 VPC 控制台。在提到 Virtual Private Cloud 的导航窗格中，选择页面左侧的 Managed Prefix Lists.，然后在新选项卡中将其打开。
6. 选择与您当前区域的 DynamoDB 对应的前缀列表名称。复制前缀列表 ID。
7. 关闭此选项卡。
8. 在 Outbound rules 选项卡上，选择 Edit outbound rules。
9. 删除现有的出站规则。
10. 选择 Add rule 并填写以下选项：
    - 输入：Custom TCP
    - 端口范围：443
    - 目的地：{Your copied prefix list id}
11. 选择 Save rules
12. 运行任务 1 中创建的测试事件，验证您的 Lambda 是否具有访问权限。它应该打印所有当前项目并将新项目写入表中。

您已成功完成任务 3！

---

来自 <https://jam.awsevents.com/event-10-20/challenges/cut-the-internet-cord?i=3&t=details-3>