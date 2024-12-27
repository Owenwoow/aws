# 规模很好

### 概述

-----

##### 客户有一个业务需求，即数据生产者应用程序需要将数据放入 Amazon Kinesis 数据流中。数据流中的数据由 Lambda 函数使用。此挑战的用户需要验证应用程序的扩展和故障处理要求，并相应地配置 Kinesis 数据流和 Lambda 函数。

## 任务 1：按比例拆分


设想监控分片数据处理指标的管理应用程序发现最后一个分片的数据处理存在延迟，因为有大量数据指向该分片。为了减少数据重定向到最后一个分片，决定拆分分片。

你的任务您必须分割Kinesis 数据流的最后一个分片。

存货Kinesis 数据流（名称`KinesisStreamForJam`：）您应该使用的服务和工具Kinesis 数据流AWS CLI任务验证配置完成后，任务将自动验证，或者您可以点击此页面顶部的“检查我的进度”按钮运行 Lambda 函数。

### 线索

### **罚分：3分**线索 1：搜索 AWS CLI 命令

请浏览 **[[Kinesis 的 AWS CLI 命令参考 ](https://docs.aws.amazon.com/cli/latest/reference/kinesis/index.html)](https://docs.aws.amazon.com/cli/latest/reference/kinesis/index.html)**并找出适合此任务的命令。

此外，**[[设置您的 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)**以访问您的环境。

### **罚分：4分**线索 2：split-shard 命令


请阅读 **[[AWS CLI 命令 - split-shard ](https://docs.aws.amazon.com/cli/latest/reference/kinesis/split-shard.html#split-shard)](https://docs.aws.amazon.com/cli/latest/reference/kinesis/split-shard.html#split-shard)**来了解命令参数。

### **罚分：5分**线索3：分割碎片



您可以使用 AWS CLI 调用 split-shard API 来分割分片。

**步骤：**

1. 确保您的 CLI 已设置为访问您的环境。使用`CLI access`JAM 页面右上角的按钮，收集 的值`AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY`。在 CLI 上运行以下命令以开始配置：

*`aws configure`*

1. 然后系统将提示您填写一些信息。

```
AWS Access Key ID: enter value copied from AWS_ACCESS_KEY_ID`
`AWS Secret Access Key: enter value copied from AWS_SECRET_ACCESS_KEY`
`Default region name: enter value from AWS console`
`Default output format: leave blank
```

1. 运行以下命令来获取活动分片的列表以及相应的起始和结束 hask 键。

*`aws kinesis list-shards --stream-name` **`KinesisStreamForJam`***

1. 按照以下格式准备命令。

*`aws kinesis split-shard`
`--stream-name` **`name_of_stream`**
`--shard-to-split` **`shard_id_to_split`**
`--new-starting-hash-key` **`start_hash_key_of_new_shard`***

1. 运行命令时，您必须提供 Kinesis 数据流名称、分片 ID 和分片的起始哈希键作为参数。*起始哈希键必须位于要拆分的分片的起始和结束哈希键范围之间。*
2. 转到 AWS CLI 并运行下面提到的命令。

*`aws kinesis split-shard`
`--stream-name` **`KinesisStreamForJam`**
`--shard-to-split` **`shardId-000000000002`**
`--new-starting-hash-key` **`320854911280625642308916404954512140970`***

**[[参考链接](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_SplitShard.html)](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_SplitShard.html)**







### 任务 2：更新分片数量以扩大规模



设想监控分片数据处理指标的管理应用程序发现数据处理存在延迟。为了适应数据流速率的变化，决定增加分片数量。

你的任务您必须将现有数据流的 分片数增加到8。

存货`Kinesis Data Streams (name: ``KinesisStreamForJam`）您应该使用的服务和工具Kinesis 数据流AWS CLI任务验证配置完成后，任务将自动验证，或者您可以点击此页面顶部的“检查我的进度”按钮运行 Lambda 函数。

### 线索

线索 1：搜索 AWS CLI 命令


请浏览 **[[Kinesis 的 AWS CLI 命令参考 ](https://docs.aws.amazon.com/cli/latest/reference/kinesis/index.html)](https://docs.aws.amazon.com/cli/latest/reference/kinesis/index.html)**并找出适合此任务的命令。

线索2：update-shard-count命令



请阅读 **[[ AWS CLI 命令 - update-shard-count](https://docs.aws.amazon.com/cli/latest/reference/kinesis/update-shard-count.html)](https://docs.aws.amazon.com/cli/latest/reference/kinesis/update-shard-count.html)** 来了解命令参数。

线索 3：AWS CLI 命令



您可以使用 AWS CLI 调用 update-shard-count API，它将更新活动分片数。

**步骤：**

1. 按以下格式准备命令：

*`aws kinesis update-shard-count`
`--stream-name` **`name_of_stream`**
`--target-shard-count` **`desired_active_shard_count`**
`--scaling-type `**`type_of_scaling`***

1. 要运行该命令，您必须
   提供*Kinesis 数据流名称*、 *所需的活动分片数* 和操作的*扩展类型作为参数。*
2. 使用 AWS CLI 运行以下命令：

*`aws kinesis update-shard-count`
`--stream-name` **`KinesisStreamForJam`**
`--target-shard-count` **`8`**
`--scaling-type` **`UNIFORM_SCALING`***

1. 流的状态需要一些时间来更新。一旦状态显示为“活动”，任务就完成了！

**[[参考链接](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_UpdateShardCount.html)](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_UpdateShardCount.html)**







### 任务 3：不拆分扩展


设想监控分片数据处理指标的管理应用程序发现每个分片中的迭代器年龄都很高。为了适应流经数据流的速率变化，决定增加每个分片的 Lambda 调用次数。

你的任务您必须将每个分片的并发 Lambda 调用 数增加到 2。

存货Kinesis 数据流 ( `name:` `KinesisStreamForJam`)Lambda 函数 ( `name:` `LambdaFunctionForJam`)您应该使用的服务和工具AWS 控制台
Kinesis 数据流
拉姆达任务验证配置完成后，任务将自动验证，或者您可以单击此页面顶部的**“检查我的进度”**按钮来运行 Lambda 函数。

### 线索

### **罚分：3分**线索 1：跳过文档


线索2：开始吧



线索3：并行化因素



1. 点击`Open AWS Console`屏幕右上角的 localted。
2. 进入 Lambda 控制台，选择函数`LambdaFunctionForJam`并点击`Triggers`下的`Configuration`。
3. 单击`Add trigger`，`Kinesis`从下拉菜单中选择，然后选择 Kinesis Stream`KinesisStreamForJam.`
4. 展开其他设置部分。
5. 提供每个分片的并发批次的值`2`。
6. 点击`Add.`







### 任务 4：避免低吞吐量流式传输


设想监控分片数据处理指标的管理应用程序发现，非高峰时段每分钟只有 100 条记录。为了适应非高峰时段的变化，决定在调用 Lambda 之前等待一段时间。

你的任务您必须将批次大小增加到180。

存货Kinesis 数据流（名称`KinesisStreamForJam`：）Lambda 函数（名称`LambdaFunctionForJam`：）您应该使用的服务和工具AWS 控制台Kinesis 数据流拉姆达任务验证配置完成后，任务将自动验证，或者您可以点击此页面顶部的“检查我的进度”按钮运行 Lambda 函数。

### 线索

线索 1：跳过文档



请仔细阅读**[[ Lambda 消费者的 Amazon Kinesis 配置参数，](https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html#services-kinesis-params)](https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html#services-kinesis-params)**以了解各种配置选项。

线索2：开始吧



转到 Lambda 控制台并选择函数 LambdaFunctionForJam。选择`Trigger`并点击`Edit.`检查配置选项并确定所需的配置。

线索 3：完整攻略


1. 转到 Lambda 控制台并选择函数“LambdaFunctionForJam”。
2. 转到配置选项卡并选择`Triggers`。
3. 选择 Kinesis 触发器并单击`Delete`。
   警告：*确保任务 3 中添加的触发器未处于处理阶段。等待其可删除。*
4. 再次添加 Kinesis 触发器并选择 Kinesis Stream `KinesisStreamForJam.`
   *（与执行任务 3 时遵循的步骤相同）*
5. 提供`Batch size`180 的值。
6. 点击`Add.`