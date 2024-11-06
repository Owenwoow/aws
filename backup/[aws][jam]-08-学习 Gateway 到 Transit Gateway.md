#学习 Gateway 到 Transit Gateway

##任务1：
背景：
根据初始设置，您注意到某些内容阻止您访问 EC2 实例。作为 MyTCPPackets Company 的网络管理员，您必须清除块，以便 EC2 实例可以通信。
要完成此任务，您需要更新三个安全组。两个安全组将位于 VPC1 中，另一个位于 VPC2 中。您应该完成以下任务：
	1. 从本地工作站 ping VPC1 堡垒主机。您可以在这些说明左侧的 Output Properties （输出属性） 选项卡中找到堡垒主机的公有 IP 地址。
	2. 使用入门部分中提供的凭证通过 SSH 连接到 VPC1 堡垒主机。
	3. 从堡垒主机 （10.0.1.x） ping VPC1 私有子网实例 （10.0.2.x）。
	4. 使用入门部分中提供的凭证，从堡垒主机 （10.0.1.x） 通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。
	5. 虽然您还无法 ping VPC2 中的私有子网实例 （192.168.0.0/16），但请确保 VPC2 私有子网 EC2 实例安全组已正确配置，以允许 VPC1 中的私有实例 ping 此实例。
完成后，此任务将允许您使用主机的公有 IP 通过 SSH 连接到 VPC1 中的 VPC1 堡垒主机，然后通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。
质询库存
	1. VPC1 （10.0.0.0/16）：
	• 公有子网 （10.0.1.0/24）
	• 公有子网路由表
	• 互联网网关
	• 私有子网 （10.0.2.0/24）
	• 私有子网路由表
	• 堡垒主机 （10.0.1.x）
	• 堡垒主机安全组
	• 私有子网 EC2 实例 （10.0.2.x）
	• 私有子网 EC2 实例安全组
	2. VPC2 （192.168.0.0/16）：
	• 私有子网 （192.168.2.0/24）
	• 私有子网路由表
	• 私有子网 EC2 实例 （192.168.2.x）
	• 私有子网 EC2 实例安全组
	3. 中转网关
	• VPC1 中转网关挂载
	• VPC2 Transit Gateway 挂载
	• Transit Gateway 路由表
架构图
	• [链接到架构图](https://aws-jam-challenge-resources.s3.amazonaws.com/learning-tgw/Learning_Gateway_to_Transit_Gateway-Architecture_Diagram.jpg)
开始
您应该考虑做的事情：
	1. 在安全组入口规则和路由表条目中使用 VPC CIDR、子网 CIDR 或实例私有 IP 地址。
你应该避免做的事情：
	1. 根据最佳实践，禁止在安全组的入口规则中使用 All-Traffic。
	2. 根据最佳实践，所有安全组的入口规则都禁止使用零 CIDR （0.0.0.0/0）。
	3. 根据最佳实践，禁止在所有安全组的入站规则中使用所有 TCP 端口。
	4. 自动任务验证脚本将验证在整个挑战赛中是否遵守这些最佳实践。任何不合规情况都将导致任务未标记为已完成。
	5. 通过 SSH 连接到 VPC2 私有子网实例 （192.168.2.x）。
您可以参考[此文档链接](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#AddRemoveRules)，了解如何向安全组添加入站规则。
注意：在本次挑战赛中，您无需使用密钥对登录任何 EC2 实例。已在所有 VPC1 实例中创建本地账户。可以在下面的 SSH 说明中找到凭据。
SSH 说明
使用以下信息，使用其公有 IP 地址通过 SSH 连接到堡垒主机 EC2 实例。堡垒主机的公有 IP 地址可以在这些说明左侧的 Output Properties （输出属性） 选项卡中找到。
凭据
	• 用户名：participant
	• 密码：tgw123
对于 Mac 用户
	• 要通过 SSH 连接到堡垒主机，请打开终端应用程序并使用以下 SSH 命令连接到堡垒主机。注意：必须替换为堡垒机的公网 IP。bastion_host_public_ip
		○ ssh participant@{bastion_host_public_ip}
	• 成功登录堡垒主机后，在堡垒主机终端中使用以下命令通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。注意：您必须替换为 VPC1 私有子网中 EC2 实例的私有 IP（即 ）。vpc1_private_subnet_instance_private_ip10.0.2.x
		○ ssh participant@{vpc1_private_subnet_instance_private_ip}
对于 Windows 用户
	• 要通过 SSH 连接到堡垒主机，您必须使用 PuTTY 等客户端。如果您的计算机上未安装此 ID，则必须下载并安装一个。检查您的策略，确保在使用公司计算机时允许这样做。
		1. 下载并安装 PuTTY 后，启动应用程序。
		2. 在 Category （类别） 窗格中，选择 Session （会话） 并填写以下字段：
		3. 输入主机，注意：必须将 bastion_host_public_ip 替换为堡垒机的公网 IP。participant@{bastion_host_public_ip}
		4. 确保 Port （端口） 值为 22。
		5. 在 Connection type （连接类型） 下，选择 SSH 。
		6. 单击 Open 按钮
有关更多详细信息，请参阅 AWS 文档中的[连接到 Linux 实例](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-ssh)部分。
	• 成功登录堡垒主机后，使用堡垒主机的以下命令通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。注意：您必须替换为 VPC1 私有子网中 EC2 实例的私有 IP（即 ）。vpc1_private_subnet_instance_private_ip10.0.2.x
		○ ssh participant@{vpc1_private_subnet_instance_private_ip}
任务清单
VPC1 和 VPC2 安全组
您应该使用的服务
亚马逊 VPC
任务验证
您必须使用适当的入站规则更新三个安全组才能完成此任务。
找到解决方案后，该任务将自动完成。此外，您可以随时通过按任务详细信息屏幕上的 “Check my progress” 按钮来检查您的进度。
注意：根据安全最佳实践，安全组的入站规则不应允许在任何端口上使用零 cidr。因此，本次挑战赛严禁使用零 cidr 作为源，并且任务不会进入已完成状态。此外，自动任务验证脚本不会将 All TCP （所有 TCP） 和 All Traffic （所有流量） 类型的入站规则视为正确的解决方案。
提示：使用 ICMP（所有 ICMP - IPv4）和 SSH 端口足以完成此任务。不需要其他端口。
蛛丝马迹
罚分： 3 点
线索 1：为安全组添加 Ingress 规则

隐藏

打开 VPC1 私有和公有实例安全组上的 ICMP 和 SSH 端口。此外，您还应在 VPC2 私有实例安全组上打开 ICMP 端口。
注意：您无需修改/添加任何出口规则。
安全组是有状态防火墙。您可以在此[链接](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules)中找到有关安全组规则的更多信息。
罚分： 4 点
线索 2：演练步骤

隐藏

演练
更新 VPC1 堡垒机安全组
	1. 在 AWS 管理控制台中，搜索 VPC 服务并访问 VPC 控制台。
	2. 在左侧面板上，单击 Security grouping（安全分组）下的 Security Groups（安全组）。
	3. 搜索名为 VPC1-BastionHost-SG 的安全组，然后单击安全组 ID。
	4. 在页面底部，单击 Inbound Rules 选项卡和 Edit Inbound Rules。
	5. 单击 Add Rule。
	6. *在类型下拉列表中选择 SSH，然后在源中选择我的 IP。添加您选择的自定义描述，例如“从我的工作站进行 SSH 访问”。
	7. 重复上述步骤以添加 ICMP 规则。
提示：对于 Type 下拉列表，选择 All ICMP - IPV4。添加您选择的自定义描述，例如“从我的工作站访问 ICMP”。
	8. 单击 Save rules（保存规则）。
更新 VPC1 私有子网 EC2 实例安全组
	1. 在 AWS 管理控制台中，搜索 VPC 服务并访问 VPC 控制台。
	2. 在左侧面板上，单击 Security grouping（安全分组）下的 Security Groups（安全组）。
	3. 搜索名为 VPC1-Pvt-Subnet-Instance-SG 的安全组，然后单击安全组 ID。
	4. 在页面底部，单击 Inbound Rules 选项卡和 Edit Inbound Rules。
	5. 单击 Add Rule。
	6. *在 Type （类型） 下拉菜单中选择 SSH。选择源中的自定义后，搜索 VPC1-BastionHost-SG。添加您选择的自定义描述，例如“从堡垒主机进行 SSH 访问”。
添加此规则将允许公有子网中的堡垒主机通过 SSH 端口与 VPC1 私有子网实例通信。这将启用从堡垒主机到私有子网实例的 SSH。
注意：使用安全组 ID 作为源被视为最佳实践。或者，您也可以提供整个公有子网的 CIDR 范围 （10.0.1.0/24） 或堡垒主机 EC2 实例的私有 IP 地址（例如，格式：10.0.1.x/32）。根据最佳实践，禁止使用 zero-cidr（0.0.0.0/0） 和 All Traffic。
	7. 重复上述步骤以添加 ICMP 规则。
提示：对于 Type 下拉列表，选择 All ICMP - IPV4。添加您选择的自定义描述，例如“从堡垒主机访问 ICMP”。
	8. 单击 Save rules（保存规则）。
更新 VPC2 私有子网 EC2 实例安全组
	1. 在 AWS 管理控制台中，搜索 VPC 服务并访问 VPC 控制台。
	2. 在左侧面板上，单击 Security grouping（安全分组）下的 Security Groups（安全组）。
	3. 搜索名为 VPC2-Pvt-Subnet-Instance-SG 的安全组，然后单击安全组 ID。
	4. 在页面底部，单击 Inbound Rules 选项卡和 Edit Inbound Rules。
	5. 单击 Add Rule。
	6. 在 Type 下拉列表中选择 All ICMP - IPV4。在选择“在源中自定义”的情况下，提供整个 VPC1 私有子网的 CIDR 范围 （10.0.2.0/24） 或 VPC1 私有子网 EC2 实例的私有 IP 地址（例如，格式：10.0.2.x/32）。添加您选择的自定义描述，例如“从 VPC1 私有子网 EC2 实例进行 ICMP 访问”。
添加此规则将允许 VPC1 私有子网中的 EC2 实例通过 ICMP 端口对 VPC2 私有子网实例执行 ping 操作。
注意：启用 ICMP 访问足以完成此质询。您无需通过 SSH 连接到 VPC2 的私有实例。根据最佳实践，此质询中禁止使用 zero-cidr（0.0.0.0/0） 和 All Traffic。




	7. 单击 Save rules（保存规则）。
自动验证脚本会将您的任务标记为已完成。或者，您可以单击 Jam 门户任务页面上的 Check my progress 按钮。

来自 <https://jam.awsevents.com/event-10-23/challenges/learning-tgw?i=1&t=details-1>




任务2：
背景
现在您可以通过堡垒主机 （10.0.1.x） 登录到 VPC1 的私有实例 （10.0.2.x），需要路由来 ping VPC2 的私有子网 （192.168.2.x） 中的实例。确定实现此要求的方法。
您应该考虑做的事情：
	1. 在路由表条目中使用 VPC CIDR、子网 CIDR 或实例私有 IP 地址。
你应该避免做的事情：
	1. 根据最佳实践，禁止在所有路由表的路由条目中使用零 CIDR （0.0.0.0/0）。自动任务验证脚本将在整个挑战赛中验证此条件。任何不合规情况都将导致任务被评估为未完成。
SSH 说明
使用以下信息，使用其公有 IP 地址通过 SSH 连接到堡垒主机 EC2 实例。您可以在这些说明左侧的 Output Properties （输出属性） 选项卡中找到堡垒主机的公有 IP 地址。
凭据
	• 用户名：participant
	• 密码：tgw123
对于 Mac 用户
	• 要通过 SSH 连接到堡垒主机，请打开终端应用程序并使用以下 SSH 命令连接到堡垒主机。注意：必须替换为堡垒机的公网 IP。bastion_host_public_ip
		○ ssh participant@{bastion_host_public_ip}
	• 成功登录堡垒主机后，在堡垒主机终端中使用以下命令通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。注意：您必须替换为 VPC1 私有子网中 EC2 实例的私有 IP（即 ）。vpc1_private_subnet_instance_private_ip10.0.2.x
		○ ssh participant@{vpc1_private_subnet_instance_private_ip}
对于 Windows 用户
	• 要通过 SSH 连接到堡垒主机，您必须使用 PuTTY 等客户端。如果您的计算机上未安装此 ID，则必须下载并安装一个。检查您的策略，确保在使用公司计算机时允许这样做。
		1. 下载并安装 PuTTY 后，启动应用程序。
		2. 在 Category （类别） 窗格中，选择 Session （会话） 并填写以下字段：
		3. 输入主机，注意：必须将 bastion_host_public_ip 替换为堡垒机的公网 IP。participant@{bastion_host_public_ip}
		4. 确保 Port （端口） 值为 22。
		5. 在 Connection type （连接类型） 下，选择 SSH 。
		6. 单击 Open 按钮
有关更多详细信息，请参阅 AWS 文档中的[连接到 Linux 实例](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html#putty-ssh)部分。
	• 成功登录堡垒主机后，使用堡垒主机的以下命令通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。注意：您必须替换为 VPC1 私有子网中 EC2 实例的私有 IP（即 ）。vpc1_private_subnet_instance_private_ip10.0.2.x
		○ ssh participant@{vpc1_private_subnet_instance_private_ip}
开始
下面链接了可能有助于执行此任务所需步骤的 AWS 文档。
	• [在路由表中添加和删除路由](https://docs.aws.amazon.com/vpc/latest/userguide/WorkWithRouteTables.html#AddRemoveRoutes)
	• [在中转网关和 VPC 之间添加路由](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html#step-add-routes)
库存
VPC1 和 VPC2 路由表
您应该使用的服务
亚马逊 VPC
任务验证
要完成此任务，您必须使用适当的路由条目更新两个路由表。
找到解决方案后，任务将自动完成。但是，您始终可以通过按任务详细信息屏幕上的“检查我的进度”按钮来检查您的进度。
注意：根据安全最佳实践，路由表不应将零 CIDR 条目作为内部流量的目标。因此，禁止在路由条目中使用零 CIDR 作为目标。自动任务验证功能将验证是否符合此要求。此外，自动任务验证脚本将在将任务标记为已完成之前验证您的路由条目。
提示：使用所需的路由条目更新 VPC 路由表就足以完成此任务。不需要使用其他服务。
蛛丝马迹
罚分： 3 点
线索 1：给路由表添加路由

隐藏

需要到中转网关的路由条目才能将流量从 VPC1 路由到 VPC2，反之亦然。
在 VPC1 和 VPC2 私有子网路由表上添加目标为中转网关的路由条目，并具有适当的目标 CIDR 块。
路由表包含一组称为路由的规则，用于确定来自子网或网关的网络流量的定向位置。[此文档链接](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)提供了有关路由表和路由条目的更多信息。

来自 <https://jam.awsevents.com/event-10-23/challenges/learning-tgw?i=2&t=details-2>


任务三
背景
成功添加所需的路由条目后，您应该找到一种方法，通过中转网关从 VPC1 私有子网 EC2 实例 （10.0.2.x） 成功 ping VPC2 私有子网 （192.168.2.x） 中的 EC2 实例。想办法完成这个要求。
此任务将通过 AWS Transit Gateway 在 VPC1 和 VPC2 之间建立连接。完成后，您在 AWS Transit Gateway 上的概念验证将可供贵公司的领导团队审查。
注意：请勿创建静态路由来完成此操作。
要完成质询，您应该完成以下任务：
	1. 从 VPC1 私有子网实例 ping VPC2 私有子网实例的私有 IP。
注意：您无需登录 VPC2 私有子网实例即可完成此挑战。
开始
您可以使用这些文档链接来了解 Transit Gateway 概念。
	• [中转网关概念](https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html#concepts)
	• [中转网关的工作原理](https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html)
您可以使用[此文档链接](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-getting-started.html#step-test-tgw)中的步骤 4 获取有关测试 Transit Gateway 的更多信息。
在向 VPC2 私有子网 EC2 实例 （192.168.2.x） 发起 ping 命令之前，请确保在 VPC1 私有子网 EC2 实例 （10.0.2.x） 中，然后再向 VPC2 私有子网实例 （192.168.2.x） 发起 ping 命令。
	• VPC 中转网关
	• VPC Transit Gateway 挂载
	• VPC Transit Gateway 路由表
您应该使用的服务
	• 亚马逊 VPC
	• AWS 中转网关
任务验证
找到解决方案后，通过 SSH 连接到 VPC1 堡垒主机。然后，使用 SSH 说明中提供的本地凭证通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。然后，您将从 VPC1 私有子网 EC2 实例 （10.0.2.x） 向 VPC2 私有子网 EC2 实例 （192.168.2.x） 发出 ping 命令。如果您的解决方案正确，您应该能够通过 Transit Gateway 成功将 ICMP 流量从 VPC1 发送到 VPC2。请继续 ping 实例至少 5 到 10 分钟。
自动任务验证脚本大约需要 5 到 10 分钟来分析传出 Transit Gateway 的数据包数量，以评估解决方案的正确性。
从 VPC1 私有子网 ping VPC2 私有子网实例后，任务将自动完成。但是，您始终可以通过按任务详细信息屏幕上的“检查我的进度”按钮来检查您的进度。
蛛丝马迹
罚分： 7 点
线索 1：在 Transit Gateway 路由表上创建路由传播

隐藏

需要在中转网关[路由](https://docs.aws.amazon.com/vpc/latest/tgw/how-transit-gateways-work.html#tgw-routing-overview)表上为每个中转网关挂载创建路由传播。这些路由是数据包通过中转网关所必需的。
您可以在下面的链接中找到有关 Transit Gateway 路由的更多信息。
路由
您可以在下面的链接中找到有关创建路由传播的更多信息。
[将路由传播到中转网关路由表](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-route-tables.html#enable-tgw-route-propagation)
罚分： 9 点
线索 2：详细演练

隐藏

演练
为 VPC1 中转网关挂载创建路由传播
	1. 在 AWS 管理控制台上，搜索 VPC 服务并访问 VPC 控制台。
	2. 在左侧的导航窗格中，选择 Transit gateways 分组下的 Transit gateway route tables。
	3. 选择中转网关路由表。
	4. 选择 Actions （操作） 下拉列表，然后选择 Create propagation （创建传播）。
	5. 在 Create propagation （创建传播） 页面上，选择 VPC1-TGW-Attachment。
	6. 选择 Create propagation （创建传播）。
为 VPC2 中转网关挂载创建路由传播
	1. 在 AWS 管理控制台上，搜索 VPC 服务并访问 VPC 控制台。
	2. 在左侧的导航窗格中，选择 Transit gateways 分组下的 Transit gateway route tables。
	3. 选择中转网关路由表。
	4. 选择 Actions （操作） 下拉列表，然后选择 Create propagation （创建传播）。
	5. 在 Create propagation （创建传播） 页面上，选择 VPC2-TGW-Attachment。
	6. 选择 Create propagation （创建传播）。
测试连接性
	1. 通过 SSH 连接到 VPC1 堡垒机。
	2. 从堡垒主机，使用 SSH 说明中提供的凭证通过 SSH 连接到 VPC1 私有子网实例 （10.0.2.x）。
	3. 成功登录后，从 VPC1 私有子网 EC2 实例 （10.0.2.x） 向 VPC2 私有子网 EC2 实例 （192.168.2.x） 发出 ping 命令。
自动任务验证脚本大约需要 5 到 10 分钟来分析传出 Transit Gateway 的数据包数量，以评估解决方案的正确性。
从 VPC1 私有子网实例 ping VPC2 私有子网实例后，该任务将自动完成。此外，您可以随时通过点击挑战详情屏幕上的“查看我的进度”按钮来查看您的进度。

来自 <https://jam.awsevents.com/event-10-23/challenges/learning-tgw?i=3&t=details-3>