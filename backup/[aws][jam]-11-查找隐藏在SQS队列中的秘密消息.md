
![image-20241020090503948](https://github.com/user-attachments/assets/9eddcc11-ebf9-4357-b21a-dfab5756a528)


**查找隐藏在SQS队列中的秘密消息！！**



### 任务 1：为什么 lambda 函数超时？

![image-20241020090727875](https://github.com/user-attachments/assets/e7470166-5294-4580-96a5-91a8bb4b36b3)


![image-20241020091242378](https://github.com/user-attachments/assets/5fc06da6-474d-48d5-8ee4-a22cca2fe7ab)


线索 2：如何访问我的 VPC 端点？

更新附加到 VPC 端点的安全组的入站规则。

- 打开 Amazon VPC 控制台https://console.aws.amazon.com/vpc/
- 在导航窗格中，选择 Endpoints。
- 找到连接到私有 VPC 的 SQS VPC 端点并打开连接到它的安全组。
- 编辑入站规则并单击添加新规则。
- 在入站规则和保存规则中提供以下详细信息。
  - 类型：https
  - 来源：定制
  - 源值：`lambda-sg`
- 打开 Amazon Lambda 控制台https://console.aws.amazon.com/lambda/
- 创建示例测试事件并测试 jam-challenge-lambda 函数。请参阅[[如何从 AWS 控制台测试 lambda 函数](https://docs.aws.amazon.com/lambda/latest/dg/testing-functions.html)](https://docs.aws.amazon.com/lambda/latest/dg/testing-functions.html)。
- 执行 lambda 函数。
- 一旦超时错误解决，并且您会收到另一个与权限相关的错误，请单击`check my progress`JAM 控制台。

![image-20241020092602348](https://github.com/user-attachments/assets/54e9513b-3cb4-404d-b144-fcb884f458cd)






### 任务 2：AWS lambda 函数角色是否具有足够的权限来访问 SQS？

![image-20241020092652885](https://github.com/user-attachments/assets/325425d2-cd27-48df-bf06-ec612504d20b)

![image-20241020093333444](https://github.com/user-attachments/assets/ca531fe0-c8b5-41f6-a9a4-861072736147)

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sqs:ReceiveMessage",
                "sqs:SendMessage",
                "sqs:GetQueueUrl",
                "sqs:ListQueues"
            ],
            "Resource": [
                "arn:aws:sqs:us-west-2:140619006867:Jam-Challenge-Queue",
                "arn:aws:sqs:us-west-2:140619006867:ResultQueue"
            ]
        }
    ]
}

```

![image-20241020094115760](https://github.com/user-attachments/assets/ee87fefc-bc34-45e9-a623-719cb631656a)
