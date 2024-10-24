![image-20241023134935482](./img/image-20241023134935482.png)

任务1：

![image-20241023140335118](./img/image-20241023140335118.png)

![image-20241023140536450](./img/image-20241023140536450.png)

dig +short task-a.aws.internal TXT @192.168.0.76



![image-20241023140723574](./img/image-20241023140723574.png)

**你的任务：**

有 2 个拥有 IAM 账户的用户，USER-A 和 USER-B。您的任务是：

1. 完全限制用户 B 查看或与名称包含以下内容的存储桶中的任何数据进行交互`patientdata`
2. 授予用户 A 对该存储桶中所有数据的完全访问权限。





```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT-ID:user/USER-A"
      },
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    },
    {
      "Effect": "Deny",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT-ID:user/USER-B"
      },
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::your-bucket-name/patientdata/*"
    }
  ]

```































