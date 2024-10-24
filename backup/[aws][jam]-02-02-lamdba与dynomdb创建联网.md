线索 1: 使用 VPC 终端节点策略限制对 DynamoDB 表的访问

当 AWS Lambda Function 在 VPC 内运行时，它不会始终接到一个安全组，该安全组必须配置适当的出站规则才能允许与目标服务进行通信。托管前缀列表是一个或多个CIDR 块的集合，使用前缀列表可以简化安全组的设置和管理。

线索 2: 解决挑战的分步说明

更新安全组规则

要更新 reinvent-lambda-sg 安全组规则，首先我们需要知道 DynamoDB 前缀列表 ID 是什么。

使用控制台查看 AWS 管理的前缀列表

通过 https://console.aws.amazon.com/vpc/ 打开亚马逊 VPC 控制台。
在导航窗格中，选择托管前缀列表。
请注意 com.amazonaws.region.dynamodb 的前缀列表 ID，其中 region 是您当前登录的区域 ID。
现在让我们更新安全组规则

打开 Amazon EC2 控制台，网址为 https://console.aws.amazon.com/ec2/
在导航窗格中，选择 reinvent-lambda-sg 安全组。
选择安全组。
选择操作，编辑出站规则，更新出站流量的规则。
单击 “添加规则”。
类型: Custom TCP。
端口范围: 443。
目的地: Custom 然后在下一个字段框中，选择你在上一步中记下的 Prefix list ID。
选择 “保存规则”。
完成后，请前往 Lambda 控制台执行 putItemLambdaFunction。

<!-- ##{"script":"<script src='https://blog.meekdai.com/Gmeek/plugins/GmeekTOC.js'></script>"}## -->
