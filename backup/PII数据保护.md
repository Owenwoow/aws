# **保护 PII 数据**

### 概述

领先的电子商务公司 ANY Inc. 最近迁移到 AWS 云，并开始使用 Amazon DynamoDB 存储客户订单。该表存储客户订单数据，包括敏感的个人身份信息 (PII)，例如姓名、地址和付款详细信息。您是 ANY INC. 的安全专家，您的任务是确保使用最小权限原则严格控制对 DynamoDB 中客户数据表的访问。您需要确保为每个应用程序创建自定义角色，定义它们对 DynamoDB 表的精确权限。为了进一步增强安全性，您必须在 DynamoDB 中实施细粒度的访问控制策略，允许特定应用程序根据用户的角色和请求上下文仅访问它们所需的数据。通过实施这些政策，ANY 的客户可以放心，他们的敏感信息受到全面的、基于角色的访问控制系统的保护。



## 任务 1：修复数据库查询访问被拒绝

------

## 背景

您的 AWS 账户中有一个名为 OrderTable 的 Dynamodb 表，主键为 CustomerID。API 正在尝试通过名为 AWSLambdaFunction 的 AWS lambda 查询 DynamoDB 表。此 lambda 被分配了一个名为 lambda_execution_role 的 IAM 角色，并附加了策略名称 JAMAWSLambdaPolicy。但是，此 API 调用返回访问被拒绝，lambda 无权执行查询。您可以尝试使用 lambda“QueryDynamoDBLambdaFunction”，可以调用它并查看结果以供参考

### 你的任务

为了完成任务，请修复此 lambda 尝试查询 dynamodb 表时被拒绝访问的问题。



### 线索3：Lambda 角色

1. 打开[[Amazon Lambda 控制台](https://console.aws.amazon.com/lambda)](https://console.aws.amazon.com/lambda) 。在导航窗格中，选择您的 Lambda。

2. 从功能列表中选择`AWSLambdaFunction`。

3. 在权限子部分中选择配置选项卡

4. 选择角色名称，它将打开 IAM 角色`lambda_execution_role`。

5. 在权限选项卡中展开附加到它的策略名称`JAMAWSLambdaPolicy`。

6. 编辑策略并添加操作`dynamodb:Query`或用以下几行替换操作语句。

   ```
   "Action": [
     "dynamodb:BatchGetItem",
     "dynamodb:GetItem",
     "dynamodb:PutItem",
     "dynamodb:Query"
   ]
   ```







## 任务 2：将数据插入 Amazon Dynamo db

------

## 背景

您的 AWS 账户中有一个为您创建的 Dynamodb 表，名为 OrderTable。表为空，主键为`CustomerID`。您可以尝试使用 lambda `QueryDynamoDBLambdaFunction`，可以调用它并查看结果以供参考。您可以在完成此任务之前运行此 lambda，并可以看到该表为空，任务完成后，您可以看到插入的项目。

### 你的任务

为了完成任务，您需要在名为 OrderTable 的空表中插入两个项目。

详情如下：-

```
item-1, 
{
 "CustomerID": "Customer1",
 "OrderID": 1,
 "CommonAttribute_OrderTotal": 254,
 "RestrictedAttribute_PII_Data": "No 123, street 15, India"
}
and item-2, 
{
 "CustomerID": "Customer2",
 "OrderID": 2,
 "CommonAttribute_OrderTotal": 334,
 "RestrictedAttribute_PII_Data": "No 456, street 145, NZ"
}
```











## 任务 3：将数据库查询结果限制为客户特定的数据

### 背景

现在您已经解决了任务 1，API 将根据任何 CustomerID 值（例如 Customer2 或 Customer1）返回查询结果。使用 lambda 查询的示例如下所示。

响应 = 客户端.查询（TableName="OrderTable", Select="SPECIFIC_ATTRIBUTES", KeyConditionExpression='CustomerID = :CustomerID', ProjectionExpression="CustomerID,CommonAttribute_OrderTotal,OrderID", ExpressionAttributeValues={ ':CustomerID': {'S': 'Customer2'}}）

DynamoDB 表具有以下属性

1. CustomerID（表的主键）例如 Customer1、Customer2
2. 订单 ID 例如 1, 45
3. CommonAttribute_OrderTotal 例如 250 INR、120 INR
4. RestrictedAttribute_PII_Data 例如 23 号，15 号街

### 你的任务

为了完成任务，添加基于属性的限制，以便此 API 仅在通过 CustomerID =“Customer1”查询时返回结果。



------

1. 打开[[Amazon lambda 控制台](https://console.aws.amazon.com/lambda)](https://console.aws.amazon.com/lambda) 。在导航窗格中，选择您的 Lambda。

2. 从功能列表中选择`AWSLambdaFunction`。

3. 在权限子部分中选择配置选项卡

4. 选择角色名称，它将打开 IAM 角色`lambda_execution_role`。

5. 在权限选项卡中展开附加到它的策略名称`JAMAWSLambdaPolicy`。

6. 编辑策略并在策略语句中添加条件

   ```
    "ForAllValues:StringEquals": { "dynamodb:LeadingKeys": "Customer1" }
   ```

   或用以下行替换条件语句（您可以利用在线策略编辑器）。

   ```
   "Condition": {
       "StringEqualsIfExists": {
           "dynamodb:Select": "SPECIFIC_ATTRIBUTES"
       },
       "ForAllValues:StringEquals": {
           "dynamodb:LeadingKeys": "Customer1"
       }
   }
   ```





### 任务 4：保护 PII 数据

------

## 背景

现在您已成功将查询结果限制为特定的 CustomerID，即 Customer1，但是此查询返回该行中的所有属性，即“RestrictedAttribute_PII_Data”。

### 你的任务

为了完成任务，添加一个基于属性的限制，以便该查询返回该行中除“RestrictedAttribute_PII_Data”之外的所有属性。

### 入门

AWS Identity and Access Management (IAM) 是一项 AWS 服务，可帮助管理员安全地控制对 AWS 资源的访问。在 DynamoDB 中，您可以选择在使用 IAM 策略授予权限时指定条件。除了控制对 DynamoDB API 操作的访问之外，您还可以控制对各个数据项和属性的访问。例如，您可以执行以下操作：

1. 授予表的权限。
2. 隐藏信息，以便用户只能看到部分属性。

有关如何控制对单个数据项和属性的访问的详细 AWS 文档，请参阅[[AWS 公共文档](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html)



### 2：属性限制的 IAM 策略

------

1. 打开[[Amazon Lambda 控制台](https://console.aws.amazon.com/lambda)](https://console.aws.amazon.com/lambda) 。在导航窗格中，选择您的 Lambda。

2. 从功能列表中选择`AWSLambdaFunction`。

3. 在权限子部分中选择配置选项卡

4. 选择角色名称，它将打开 IAM 角色`lambda_execution_role`。

5. 在权限选项卡中展开附加到它的策略名称`JAMAWSLambdaPolicy`。

6. 编辑策略并在策略语句中添加条件` "dynamodb:Attributes": [ "CustomerID", "OrderID", "CommonAttribute_OrderTotal" ] }`或用以下几行替换条件语句。

   ```
     "Condition": {
         "StringEqualsIfExists": {
             "dynamodb:Select": "SPECIFIC_ATTRIBUTES"
         },
         "ForAllValues:StringEquals": {
             "dynamodb:LeadingKeys": "Customer1",
             "dynamodb:Attributes": [
                 "CustomerID",
                 "OrderID",
                 "CommonAttribute_OrderTotal"
             ]
         }
     }
   ```

7. 您更新后的保单条件应如上所示。