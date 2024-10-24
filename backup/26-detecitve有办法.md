![image-20241021182911470](./img/image-20241021182911470.png)

### 任务 1：Amazon CloudWatch 日志的数据保护策略不正确

![image-20241021183054683](./img/image-20241021183054683.png)

### 解答：

#### 线索 1：识别日志中暴露的 PID 信息



查看与名为 的 CloudWatch 日志组关联的数据保护策略。`sensitive-data-01-lambda`

通过分析此日志组的 CloudWatch 日志，您可以确定正在记录的特定类型的个人身份数据 （PID）。确定 PID 数据类型后，请应用日志组的**“数据保护”**部分中找到的相应数据保护策略。

有关更多信息，请参阅[文档](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/mask-sensitive-log-data.html)



#### 线索 2：分步指导解决挑战



要应用正确的数据保护策略，请执行以下步骤：

**步骤#1：**确定日志文件中公开的 PID 信息

- 转到 **AWS 控制台**
- 导航到 [AWS CloudWatch](https://console.aws.amazon.com/cloudwatch/) 控制台。
  - 在左侧窗格中，单击 **Logs** undrer **the CloudWatch**，然后单击 **Log groups**
    - 单击 **log group（日志组**）`/aws/lambda/sensitive-data-01-lambda`
    - 向下滚动到 **Log streams**
    - 点击类似于 **YYYY/MM/DD/[$LATEST]xxxxxxxxxx** 的日志流
  - 在 **Filter events** 中键入字符串，然后按 *Enter* 键`foobar.com`
  - 单击*菱形*箭头以展开日志
    - 您将在日志文件中找到 Expos。`email address: xxxxx.xxx@foobar.com`
    - 日志当前公开 PID 信息，例如`Email Address`
- 从顶部单击`/aws/lambda/sensitive-data-01-lambda log group`
- 向下滚动并单击选项卡`Data protection`
- 检查 **Data identifiers** 的值。它显示为 。这意味着*保护策略*适用于字段。`Address``Address`


**注意：**忽略错误`There was an error fetching CloudWatch metrics`

**步骤#2：**应用**数据保护策略**以保护 *EmailAddress*

- 转到 **AWS 控制台**
- 导航到 [AWS CloudWatch](https://console.aws.amazon.com/cloudwatch/) 控制台。
  - 在左侧窗格中，单击 **Logs** undrer **the CloudWatch**，然后单击 **Log groups**
  - 单击 **log group（日志组**）`/aws/lambda/sensitive-data-01-lambda`
- 单击 **Data protection** 选项卡
- 单击 **Actions** 按钮，然后选择 **Edit policy**
- 单击 **Auditing and masking configuration** 下的 **Data identifiers** 下的下拉列表
  - 从下拉列表中选择选项`EmailAddress`
- 单击 **Save changes（保存更改**）
- 要验证是否应用了正确的数据保护策略，请转到 **Log stream （日志流**） 选项卡，然后单击类似于的日志流，然后滚动到底部`YYYY/MM/DD/[$LATEST]xxxxxxxxxx`
- 您现在可以发现电子邮件地址数据已被屏蔽为`Random email address: ***********************`

**请注意，在应用数据保护策略后，只有具有 PID 信息的新日志数据才会被屏蔽**



### 任务2



![image-20241021183344343](./img/image-20241021183344343.png)

#### 线索 1：识别日志文件中的 PID 信息字段

检查是否有与名为`sensitive-data-02-lambda`

在查看此日志组的 CloudWatch 日志时，您可以确定正在记录的特定类型的 PID 数据。

您会发现以下内容：

- `Postal code (ZipCode-US)`
- `Date Of Birth`

PID 信息将记录在此日志组中。

#### 线索 2：分步指导解决挑战

要应用正确的数据保护策略，请执行以下步骤：

**步骤#1：**确定日志文件中公开的 PID 信息

- 转到 **AWS 控制台**
- 导航到 [AWS CloudWatch](https://console.aws.amazon.com/cloudwatch/) 控制台。
  - 在左侧窗格中，单击 **CloudWatch** 下的 **Logs**，然后单击 **Log groups**
  - 单击 **log group（日志组**）`/aws/lambda/sensitive-data-02-lambda`
  - 向下滚动到 **Log streams**
  - 单击名称类似于 **YYYY/MM/DD/[$LATEST]xxxxxxxxxx** 的日志流
  - 在 **Filter events** 中键入字符串，然后按 *Enter* 键`"date of birth"`
  - 单击*菱形*箭头以展开日志
    - 您将在日志文件中找到 Expos。`date of birth: xx-xx-xxxx`
    - 日志当前公开 PID 信息，例如`Date of Birth`
  - 清除 **Filter events** 中的 Filter 和 Type 字符串，然后按 *Enter* 键`"Zip Code"`
  - 单击*菱形*箭头以展开日志
    - 您将在日志文件中找到 Expos。`Zip Code: xxxxxx`
    - 日志当前公开 PID 信息，例如`ZipCode`
- 从顶部单击`/aws/lambda/sensitive-data-02-lambda log group`
- 向下滚动并单击选项卡`Data protection - new`
- 检查 **Data identifiers** 的值。当前没有设置数据标识符

**注意：**忽略错误`There was an error fetching CloudWatch metrics`

**步骤#2：**应用**数据保护策略**以保护 *DateofBirth* 和 *ZipCode*

- 转到 **AWS 控制台**
- 导航到 [AWS CloudWatch](https://console.aws.amazon.com/cloudwatch/) 控制台。
  - 在左侧窗格中，单击 **CloudWatch** 下的 **Logs**，然后单击 **Log groups**
  - 单击 **log group（日志组**）`/aws/lambda/sensitive-data-02-lambda`
- 转到 **数据保护 - 新**选项卡，然后单击 **创建策略**
- 单击 **Auditing and masking configuration** 下的 **Data identifiers** 下的下拉列表
  - 从下拉列表中选择和选项`ZipCode-US``DateOfBirth`
- 单击 **Activate Data Protection**
- 要验证是否应用了正确的数据保护策略，请转到 **Log stream （日志流**） 选项卡，然后单击类似于的日志流，然后滚动到底部`YYYY/MM/DD/[$LATEST]xxxxxxxxxx`
- 您现在可以发现 and 数据已被屏蔽为 和`Zip code``Date of Birth``Random Zip Code: *****``Random date of birth: **********`

**请注意，在应用数据保护策略后，只有具有 PID 信息的新日志数据才会被屏蔽**