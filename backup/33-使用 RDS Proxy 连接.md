













https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy-creating.html



![image-20241022092659465](./img/image-20241022092659465.png)

![image-20241022100425578](./img/image-20241022100425578.png)

### 线索 1：创建 RDS 代理时需要什么

------

隐藏

------

RDS Proxy 有多种选择。您应该从 Lambda 函数源代码中研究应该选择什么。必须创建一个 RDS Proxy 以匹配 Lambda 函数的实现。然后，从您的 AWS 账户中查找您需要设置的资源的名称。

- [开始使用 RDS Proxy - Amazon Relational Database Service](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy-setup.html)

------



### **罚分：19分**线索2：检查网络设置

------

隐藏

------

创建 RDS Proxy 后，DBClient 函数中是否出现连接超时？您是否未能创建 RDS Proxy？要解决这一难题，仔细检查 SecurityGroup 设置非常重要。SecurityGroups 之间的关系如下图所示。在 PRSProxySecurityGroup 中创建 RDS Proxy。

![网络架构](https://aws-jam-challenge-resources.s3.amazonaws.com/lambda-with-rds-proxy/network-architecture.png)

- [授予 Lambda 函数对 Amazon VPC 中的资源的访问权限 - AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html#configuration-vpc-best-practice)

------



### **罚分：22分**线索3：完整解决方案

------

隐藏

------

在 AWS 管理控制台顶部的搜索栏中，搜索并选择`RDS`。在左侧菜单中，单击*代理*链接。单击*创建代理*按钮。*输入您选择的代理标识符*的名称。例如：`jam-proxy`。在*目标组配置*部分，`database`在列表中选择。在*身份验证*部分，输入以下内容。*身份和访问管理 (IAM) 角色*：RDSProxyRole*Secrets Manager secrets*：选择名称以`rds!`*IAM 身份验证*：必需在连接部分，输入以下内容。*选中“需要传输层安全性”*旁边的复选框。点击*附加连接配置*，删除`default`安全组，然后选择`RDSProxySecurityGroup`。等待 jam-proxy 状态变为可用。另外，选择`jam-proxy`并等待默认目标组也变为可用。复制*代理端点 URL*。格式类似于`jam-proxy.proxy-XXXXXXXX.(region).rds.amazonaws.com`。在 AWS 管理控制台顶部的搜索栏中，搜索并选择`Lambda`。单击`DBClient`功能链接。单击*“配置”*选项卡，然后单击“*环境*变量”。单击*编辑*按钮。将键的值更改`DB_Endpoint`为*Proxy Endpoint URL*。单击*“保存”*按钮。单击*“代码”*选项卡，然后单击*“测试”*按钮。单击*调用*按钮。您将收到一个密钥值，`answer`即完成挑战所需的代码。