# **排队等候！**

### 概述

背景您经营着一家电子商务初创公司，身兼数职。您已开始在 AWS 上使用无服务器计算。您即将召开一场大型投资者会议，就在会议前，您发现一位同事意外做了一些更改，导致您最先进的事件处理系统出现故障，而您原本计划在会议期间演示该系统。您需要戴上云开发人员的帽子，解决问题，并为涉及演示产品的会议做好准备。您准备好迎接挑战了吗？







### 任务 1：SNS 订阅

##### 背景

您的网站收到大量流量，这反过来又会在后端生成大量通知。这些通知会发送给不同的消费者。这就是您选择使用 Amazon Simple Notification Service (SNS) 的原因，这样许多消费者就可以决定根据自己的意愿使用通知。每个通知都需要由 AWS Lambda 函数处理。但是，您需要控制这些通知/消息流向 Lambda。这就是您决定在 SNS 和 AWS Lambda 之间使用 Amazon Simple Queue Service (SQS) 的原因。通知流程如下：

```
Notification -> SNS (MySNSTopic) -> SQS (MyQueue) -> Lambda (MyFunctionFixIT)
```

看起来您部署的所有资源都还在那里：`MySNSTopic`SNS 主题、`MyQueue`SQS 队列和`MyFunctionFixIT`Lambda 函数。但是，查看 Lambda 函数时，似乎从未调用过它。要向大投资者进行演示，您需要一直收到 Lambda 函数的通知。但是，让我们一次启动一项任务。

##### 任务一

首先，在此任务中，您必须`SNS (MySNSTopic) -> SQS (MyQueue)`通过创建对 SNS 主题的订阅来修复设置。

注意：您不必创建额外的资源。

##### 入门

您可以使用 AWS 的文档在 AWS 管理控制台中找到方法： [https://docs.aws.amazon.com/](https://docs.aws.amazon.com/)

##### 存货

- 我是
- 社交网络
- 新加坡优质服务
- 拉姆达
- 可以在输出属性中找到 ARN

##### 您应该使用的服务

- 社交网络
- 新加坡优质服务

##### 任务验证

找到解决方案后，任务将自动完成。此外，您还可以随时通过点击挑战详情屏幕中的**“检查我的进度”按钮来检查您的进度。**

 在sns中添加订阅

![image-20241104082233778](https://github.com/user-attachments/assets/225ccd39-25b3-4464-a962-efae5a972f1e)








### 任务 2：Lambda 接收消息的权限

##### 背景

请记住，您仍在尝试修复此通知流程：

```
Notification -> SNS (MySNSTopic) -> SQS (MyQueue) -> Lambda (MyFunctionFixIT)
```

通知现在进入 SQS 队列`MyQueue`。但是，Lambda 函数`MyFunctionFixIT`从未被触发。这似乎是权限问题。您可以来修复它！

##### 任务

您的任务是验证 Lambda 函数所使用的角色及其执行权限。您首先必须通过查看 Lambda 函数的配置来找到 Lambda 函数所使用的角色`MyFunctionFixIT`。然后，您需要将缺少的权限语句添加到`sqs`内联策略中。

##### 重要信息：

- 您将无法将其他策略附加到该角色。您只能修改 `sqs`内联策略。

- 忽略 IAM 控制台中与权限相关的任何错误消息`arn:aws:access-analyzer`。

- 确保遵循最小权限原则。只需添加完成任务所需的权限。如果您需要回滚更改，则只允许对 SQS 队列的

  资源

  

  ```
  MyQueue
  ```

  ARN执行以下操作：

  ```
  [
    "sqs:DeleteMessage",
    "sqs:SendMessage",
    "sqs:GetQueueAttributes"
  ]
  ```

##### 存货

- 我是
- 社交网络
- 新加坡优质服务
- 拉姆达

##### 您应该使用的服务

- 我是
- 拉姆达

##### 任务验证

找到解决方案后，任务将自动完成。此外，您还可以随时通过点击挑战详情屏幕中的**“检查我的进度”按钮来检查您的进度。**

### 解答：

### 线索一：Lambda 执行者角色策略权限

导航到 AWS 管理控制台中的 IAM 服务，并将它添加到名称中的IAM 角色的策略`sqs:ReceiveMessage`中。`sqs``LambdaExecutionRoleFixIt`



### **罚分：3分**线索2：在IAM控制台中添加以下权限


#### 更改角色允许的权限

1. 导航到 AWS 管理控制台中的 IAM 服务。

2. 在 IAM 控制台的导航窗格中，选择角色。

3. 在搜索栏中输入`LambdaExecutionRoleFixIt`。

4. 单击列表中唯一 IAM 角色的名称。

5. 要编辑现有的内联策略，请单击该策略的名称`sqs`。

6. 当光标位于屏幕右侧的*操作*列表中时，单击“SQS”，将显示操作列表。

7. 在`Filter actions`文本框中搜索`ReceiveMessage`。

8. 单击*访问级别 - 阅读*`ReceiveMessage`旁边的复选框。

9. 这会将添加到

   ```
   sqs:ReceiveMessage
   ```

   操作列表中。操作列表现在应为：

   ```
   [
    "sqs:DeleteMessage",
    "sqs:SendMessage",
    "sqs:GetQueueAttributes",
    "sqs:ReceiveMessage"
   ]
   ```

10. 点击`Next`。

11. 点击`Save changes`。

现在该任务应该已经解决。

参考文档：

- [[修改角色步骤](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-managingrole-editing-console.html#roles-modify_permissions-policy)](https://docs.aws.amazon.com/IAM/latest/UserGuide/roles-managingrole-editing-console.html#roles-modify_permissions-policy)
- [[sqs 策略](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-examples-of-iam-policies.html)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-basic-examples-of-iam-policies.html)







### 任务 3：lambda 的事件源

可能得分：27 线索罚分：0 **可用得分：27**

------

检查我的进度

------

##### 背景

请记住，您仍在尝试修复此通知流程：

```
Notification -> SNS (MySNSTopic) -> SQS (MyQueue) -> Lambda (MyFunctionFixIT)
```

通知仍未到达 Lambda 函数。您知道您的 Lambda 函数可以`ReceiveMessage`在 SQS 队列上执行。但是，消息仍在队列中未被处理。SQS 和 Lambda 之间的链接似乎已断开。您来这里就是为了修复它！

##### 任务

您的任务是修复`MyQueue`SQS 队列和 Lambda 函数 之间的触发器。您可以在https://aws.amazon.com/lambda/`MyFunctionFixIT`上找到有关 Lambda 的更多信息。

注意：不需要创建额外的资源，并且Lambda函数的代码是正确的。

##### 存货

- 我是
- 社交网络
- 新加坡优质服务
- 拉姆达

##### 您应该使用的服务

- 新加坡优质服务
- 拉姆达

##### 任务验证

找到解决方案后，任务将自动完成。此外，您还可以随时通过点击挑战详情屏幕中的**“检查我的进度”按钮来检查您的进度。**