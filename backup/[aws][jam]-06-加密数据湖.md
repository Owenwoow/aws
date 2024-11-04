# 加密数据湖

#### 概述

您是 MuMuMango 的安全工程师，负责创建数据湖来存储公司销售的所有水果。您的公司政策规定，数据湖中的敏感数据必须使用组织控制的加密密钥进行静态加密。您刚刚发现数百个对象已使用默认 S3 加密密钥上传到 S3。您的任务是快速纠正此政策违规行为，以防止对 MuMuMango 品牌造成损害，并确保将来的上传符合政策。



您的 AWS 账户具有以下资源：

- 数据湖 S3 存储桶包含数百个使用默认 S3 加密密钥加密的对象。
- 必须使用客户管理的 KMS 密钥来静态加密数据湖中的所有当前和未来对象。您可以在“输出属性”部分中找到密钥 ID。
- [[S3 库存](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-inventory.html)](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-inventory.html)文件。针对此挑战，我们为您预先创建了一个库存 CSV 文件。
- 修复加密错误时可以使用且应该使用的 IAM 角色 (FixEncryptionRole)。**注意：**您不需要承担此角色。

### 要求

您的工作是通过以下方式使您的数据湖符合政策：

1. 使用可扩展的方法，通过客户管理的 KMS 密钥加密数据湖中的现有对象。
2. 配置数据湖 S3 存储桶，以便新对象默认使用客户管理的 KMS 密钥加密。

### 建议

- 确保按要求完成上述要求，**以便**
- 虽然 S3 控制台允许您更改少量对象的加密，但此方法无法扩展到数百万或数十亿个对象。如果您不使用可扩展的方法来加密对象，质询验证将不会授予满分。
- 确保您使用的是生成的 KMS 密钥而不是 S3 KMS 密钥
- 完成任务后，点击**“检查我的进度”**即可确认您已完成挑战

### 有用的链接

以下几个链接可以帮助您应对挑战：

- [[S3 批量操作](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/batch-ops.html)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/batch-ops.html)
- [[Amazon S3 存储桶的默认加密](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html)](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html)





### 解答：

### 线索 1：使用 S3 批量操作修复未加密的对象



对于此挑战，您需要使用 S3 批量操作来加密不合规的对象。使用 FixEncryptionRole、KMS 密钥和 S3 中提供的清单文件来创建批量操作作业。有关更多详细信息，请参阅此博客文章：

- [[使用批处理操作加密 S3](https://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/)](https://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/)



### 线索 2：创建 S3 批处理作业


以下是创建 S3 批处理作业的步骤：

1. 转到 S3 控制台中的“批量操作”选项卡并创建一个作业。
2. 确保所选区域与存储桶所在的位置相同。（您可以通过单击 S3 中的存储桶并查看屏幕右侧来确认这一点）。
3. 清单文件为 CSV 格式，位于encrypt-data-lake-{accountid} 存储桶的**报告文件夹中。**
4. 这是一个复制操作。**选择**encrypt-data-lake-{accountid}/data 作为目标。无需向目标添加前缀，因为我们想要覆盖每个对象的清单文件中的路径。**确认**对象将被覆盖。
5. 仍然在操作步骤中，在服务器端加密部分中选择**指定加密密钥**，然后选择**覆盖目标存储桶设置进行默认加密**，然后选择**AWS Key Management Service 密钥（SSE-KMS）**，然后选择**从您的 AWS KMS 密钥中选择，然后从下拉菜单中选择与输出属性部分中的****KMSKeyId**匹配的 KMS 密钥 ID 。其余选项保留为默认设置。
6. 如果需要，将完成报告发送到**报告**文件夹进行调试。添加**FixEncryptionRole**作为 IAM 角色。
7. 确认输入并创建作业。
8. 准备好后，选择它并单击**运行作业**。这将带您到另一个页面，向下滚动并单击运行作业。它应该运行得非常快。



### 线索3：防止未加密的对象



我们要做的最后一件事是强制加密我们的 S3 存储桶。这是为了确保任何未加密的对象都无法上传。

1. 单击**encrypt-data-lake-{accountid}** S3 Bucket 并转到**属性**选项卡
2. 单击默认加密部分中的**编辑，然后选择****AWS 密钥管理服务密钥 (SSE-KMS)**，然后选择**从您的 AWS KMS 密钥中选择，然后从下拉菜单中选择与输出属性部分中的****KMSKeyId**匹配的 KMS 密钥 ID 。
3. 单击**“保存更改”**。

完成此操作后，我们可以确保上传到 S3 存储桶的对象是合规的。



![image-20241104095514208](https://github.com/user-attachments/assets/087004d1-639b-4841-876d-1549cdf78875)





## 答案：ArthurMorgan