**剪断互联网线！**

![image-20241020094227557](./img/image-20241020094227557.png)

## 任务 1：授予 Lambda 写访问权限！

![image-20241020094412642](./img/image-20241020094412642.png)

https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/reference_policies_examples_dynamodb_specific-table.html

Amazon DynamoDB：允许访问特定的表

![image-20241020095112298](./img/image-20241020095112298.png)

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



![image-20241020095614802](./img/image-20241020095614802.png)

#### 1.将vpc附加到lamdba

![image-20241020100631353](./img/image-20241020100631353.png)

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

![image-20241020100754308](./img/image-20241020100754308.png)

## 任务3：锁定连接！

更改安全组