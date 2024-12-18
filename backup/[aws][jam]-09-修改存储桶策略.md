### 任务 1：为您的 IAM 用户应用存储桶策略



有 2 个拥有 IAM 账户的用户，USER-A 和 USER-B。您的任务是：

1. 完全限制用户 B 查看或与名称包含以下内容的存储桶中的任何数据进行交互`patientdata`
2. 授予用户 A 对该存储桶中所有数据的完全访问权限。



### 1：检查你的 S3 存储桶策略

您检查过 S3 存储桶策略吗？那里应该定义了权限声明。



### 2：如何创建 S3 存储桶策

S3 存储桶策略由遵循特定格式的 JSON 格式语句组成。每个策略都包含效果、主体、操作和资源。效果是指语句的最终效果，例如允许某事或拒绝某事。主体是指被允许的人，例如 IAM 用户或角色（以 ARN 表示）。操作是指您允许主体执行或不执行哪些特定操作，例如 GetObject。最后，资源是指 AWS 资源。

尖端：

1. 资源是我们授予或拒绝权限的存储桶。
2. 如果您愿意，您可以在 AWS 控制台中访问策略生成器！



### 3：分步说明

1. 转到您的 AWS 控制台并搜索 S3 服务。
2. 在您的 S3 控制台中，您应该看到已经创建的不同存储桶。
3. 选择以 jam-challenge-patientdata* 开头的存储桶并记下该存储桶名称。
4. 转到权限，向下滚动并找到存储桶策略部分。
5. 点击编辑
6. 删除现有的示例内容并粘贴以下政策。
7. 使用右上角的 AWS 账号更新“Replace_with_AWS_Account_number”的值
8. 将“Replace_with_Bucket_name”替换为您在步骤 3 中捕获的存储桶名称。
9. 单击“保存更改”。

```
{
    "Version": "2012-10-17",
    "Statement": [{
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::Replace_with_AWS_Account_number:user/USER-A"
            },
            "Action": "s3:Get*",
            "Resource": [
                "arn:aws:s3:::Replace_with_Bucket_name",
                "arn:aws:s3:::Replace_with_Bucket_name/*"
            ]
        },
        {
            "Sid": "Statement2",
            "Effect": "Deny",
            "Principal": {
                "AWS": "arn:aws:iam::Replace_with_AWS_Account_number:user/USER-B"
            },
            "Action": "s3:Get*",
            "Resource": [
                "arn:aws:s3:::Replace_with_Bucket_name",
                "arn:aws:s3:::Replace_with_Bucket_name/*"
            ]
        }
    ]
}
```





### 任务 2：上传示例文件并启用 S3 访问日志

------

1. 从这里下载示例患者数据文件[[。](https://aws-jam-challenge-resources.s3.amazonaws.com/s-3-accesslogging-monitoring/patient.csv)](https://aws-jam-challenge-resources.s3.amazonaws.com/s-3-accesslogging-monitoring/patient.csv)
2. 转到您的 AWS 控制台并搜索 S3 服务。
3. 在您的 S3 控制台中，您应该看到已经创建的不同存储桶。
4. 选择以 jam-challenge-patientdata* 开头的存储桶
5. 点击上传
6. 点击添加文件
7. 从您的计算机中选择示例患者数据文件并单击上传。
8. 如果出现上传状态窗口，请单击“取消”。
9. 转到属性，向下滚动并找到服务器访问日志部分。
10. 点击编辑
11. 选择启用
12. 它会要求您提供目标存储桶，请单击浏览 S3
13. 选择以 jam-challenge-accesslogs* 开头的存储桶
14. 单击“保存更改”。