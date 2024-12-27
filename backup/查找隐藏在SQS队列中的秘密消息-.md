![image-20241020090503948](https://github.com/user-attachments/assets/9eddcc11-ebf9-4357-b21a-dfab5756a528)


**查找隐藏在SQS队列中的秘密消息！！**



## 任务 1：为什么 lambda 函数超时？

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






## 任务 2：AWS Lambda 函数角色是否具有足够的权限来访问 SQS？

------

### 背景

成功更新 VPC 安全配置后，我们执行了 Lambda，但现在它失败并出现错误：
 `when calling the GetQueueUrl operation: The specified queue does not exist or you do not have access to it`。
 应用程序（Lambda 函数）从 SQS 队列 `Jam-Challenge-Queue` 轮询（接收）消息并对其进行处理。

------

### 任务

要完成此任务，您需要确保附加到 Lambda 函数的 IAM 角色具有足够的权限来对 SQS 队列执行基本操作并获取有关 SQS 队列的详细信息。请记住在向现有策略添加/更新任何权限时遵循 **最小特权原则**。

#### 任务要求：

1. 为了验证 AWS Lambda 函数，请在 IAM 策略中进行必要的更改后执行它。
2. 如果没有遵循最小特权原则，策略更新将失败。
3. 可以更新 IAM 策略附加的 AWS Lambda 函数，以添加其他权限。

------

### 存货

我们已为您提供以下资源以帮助您成功完成挑战：

- **AWS Lambda 函数**（`Jam-Challenge-Lambda`）
- **Amazon SQS 队列**（`Jam-Challenge-Queue`）
- **IAM 角色**（`Jam-Challenge-Role`）

------

### 入门

1. 检查附加到 IAM 角色 `Jam-Challenge-Role` 的 IAM 权限。
2. 请参阅如何在 Amazon SQS 中使用基于身份的策略。

------

### 任务验证

找到解决方案后，任务将自动完成。您可以随时通过点击挑战详情屏幕中的“**查看我的进度**”按钮来查看进度。

#### 注意事项：

- 请检查 Lambda 函数代码并仅添加函数中所需的必要权限。
- 要完成任务，您必须使用示例测试事件执行 AWS Lambda 函数。
- 请遵守 **最低特权 IAM 政策**。
- 更新 IAM 策略时，忽略 AWS IAM 控制台上与 IAM 访问分析器相关的错误。
- 基于 SQS 资源的策略超出了此挑战的范围。

##### 线索 1：哪些 IAM 权限允许访问 Amazon SQS 服务？

1. 打开 [[Amazon IAM 控制台](https://console.aws.amazon.com/iam/)](https://console.aws.amazon.com/iam/)
2. 更新附加到 IAM 角色的内联策略 `Jam-Challenge-Role`。请参阅如何使用控制台更新访问策略。
3. 添加必要的权限以对 SQS 队列执行基本操作，参考此文档。
4. 仅添加获取队列属性和队列 URL 所需的权限。
5. 执行 Lambda 函数。
6. 成功执行 Lambda 函数后，点击 JAM 控制台中的“**检查我的进度**”。

##### 线索 2：更新附加到 IAM 角色的内联策略

1. 打开 [[AWS IAM 控制台](https://console.aws.amazon.com/iam/)](https://console.aws.amazon.com/iam/)
2. 在导航窗格中，选择 **角色**。
3. 搜索 IAM 角色 `Jam-Challenge-Role`。
4. 编辑客户内联政策 `LambdaExecutionRolePolicy`。
5. 请参阅如何使用 AWS 控制台更新访问策略。
6. 将以下声明添加到现有的内联策略中：

```json
{
    "Action": [
        "sqs:ReceiveMessage",
        "sqs:GetQueueUrl"
    ],
    "Resource": [
        "arn:aws:sqs:*:*:Jam-Challenge-Queue"
    ],
    "Effect": "Allow"
}
```

1. 检查添加的策略并保存更改。
2. 添加策略后，执行 AWS Lambda 函数。
3. 成功执行 Lambda 函数后，点击 JAM 控制台中的“**检查我的进度**”。

------

这样转化后的 Markdown 格式结构清晰，方便阅读并且便于理解任务要求。
