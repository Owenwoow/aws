https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy-creating.html



![image-20241022092659465](https://github.com/user-attachments/assets/73fa69ab-65b5-44de-8c6d-105c3d9a40f6)


![image-20241022100425578](https://github.com/user-attachments/assets/ae0320fc-ac92-4fca-853d-2d39fb1f99a6)


###  1：创建 RDS 代理时需要什么

RDS Proxy 有多种选择。您应该从 Lambda 函数源代码中研究应该选择什么。必须创建一个 RDS Proxy 以匹配 Lambda 函数的实现。然后，从您的 AWS 账户中查找您需要设置的资源的名称。

- [[开始使用 RDS Proxy - Amazon Relational Database Service](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy-setup.html)](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy-setup.html)

------



### 2：检查网络设置

创建 RDS Proxy 后，DBClient 函数中是否出现连接超时？您是否未能创建 RDS Proxy？要解决这一难题，仔细检查 SecurityGroup 设置非常重要。SecurityGroups 之间的关系如下图所示。在 PRSProxySecurityGroup 中创建 RDS Proxy。

![网络架构](https://aws-jam-challenge-resources.s3.amazonaws.com/lambda-with-rds-proxy/network-architecture.png)

- [[授予 Lambda 函数对 Amazon VPC 中的资源的访问权限 - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#configuration-vpc-best-practice)](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#configuration-vpc-best-practice)

### 3：完整解决方案

1. 在 AWS Management Console 顶部的搜索栏中，搜索并选择 。RDS
2. 在左侧菜单中，单击 *Proxy* 链接。
3. 点击 *Create     Proxy* 按钮。
4. 输入所选*代理标识符*的名称。例如：。jam-proxy
5. 在 *Target     group configuration* 部分中，在列表中选择。database
6. 在 *Authentication*     *（身份验证*） 部分中，输入如下所示。
   1. *身份和访问管理 （**IAM**） 角色*：RDSProxyRole
   2. *Secrets Manager* *密钥*：选择名称开头为rds!
   3. *IAM* *身份验证* ：      必需
7. 在 Connectivity （连接） 部分中，输入以下内容。
   1. 选中 *Require Transport Layer Security* 旁边的复选框。
   2. 单击 *Additional      connectivity configuration**（其他连接配置*），删除安全组，然后选择      。defaultRDSProxySecurityGroup
8. 等待 jam-proxy 状态为 Available。此外，选择并等待默认目标组也为 Available。jam-proxy
9. 复制 *Proxy     Endpoint URL*。格式类似于 。jam-proxy.proxy-XXXXXXXX.(region).rds.amazonaws.com
10. 在 AWS Management     Console 顶部的搜索栏中，搜索并选择 。Lambda
11. 单击函数链接。DBClient
12. 单击 *Configuration* 选项卡，然后单击 *Environment* variables。
13. 点击 *Edit* 按钮。
14. 将密钥的值更改为 *Proxy     Endpoint URL* *（代理端点* *URL*）。DB_Endpoint
15. 点击 *Save* 按钮。
16. 单击 *Code* 选项卡，然后单击 *Test* 按钮。
17. 单击 *Invoke* 按钮。
18. 您将收到一个 key you 完成挑战所需的代码的值。answer