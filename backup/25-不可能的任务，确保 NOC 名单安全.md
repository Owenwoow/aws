# 不可能的任务，确保 NOC 名单安全

### 任务 1：kms密钥

记得打开密钥轮换

![image-20241021161625819](./img/image-20241021161625819.png)

### 任务2：创建sns主题

### 任务3：创建s3桶

### 任务4：创建s3事件

![image-20241021181614361](./img/image-20241021181614361.png)

1. **访问 SNS 控制台**：
   - 搜索并进入 **Simple Notification Service (SNS)** 控制台。

2. **编辑 SNS 主题**：
   - 从主题列表中，点击你的 `topic_NOC_event`。

3. **修改访问策略**：
   - 向下滚动到 **Access policy**（访问策略）部分，打开 **JSON 编辑器**。
   - 在现有的 “Statement” 元素中，找到倒数第三行（在 `]` 之前），添加一个逗号，然后插入以下新条目：

   ```json
   {
     "Sid": "Permit S3 to SNS topic publish policy",
     "Effect": "Allow",
     "Principal": {
       "Service": "s3.amazonaws.com"
     },
     "Action": "SNS:Publish",
     "Resource": "arn:aws:sns:R-RRRR-R:XXXXXXXXXXXX:topic_NOC_event",
     "Condition": {
       "StringEquals": {
         "aws:SourceAccount": "XXXXXXXXXXXX"
       },
       "ArnLike": {
         "aws:SourceArn": "arn:aws:s3:*:*:secret-noc-bucket-XXXXXXXXXXXX"
       }
     }
   }
   ```

   - 确保将 `XXXXXXXXXXXX` 替换为你的 AWS 账号，将 `R-RRRR-R` 替换为你的 AWS 区域（例如 `us-west-1`）。

4. **配置 KMS 密钥**：
   - 搜索并进入 **KMS** 控制台，找到你的客户托管式密钥。
   - 编辑密钥策略，添加以下新条目：

   ```json
   {
     "Sid": "S3 to use KMS Key",
     "Effect": "Allow",
     "Principal": {
       "Service": "s3.amazonaws.com"
     },
     "Action": [
       "kms:GenerateDataKey",
       "kms:Decrypt"
     ],
     "Resource": "*"
   }
   ```

5. **配置 S3 事件通知**：
   
   - 返回 **S3 控制台**，转到你的 NOC 存储桶的 **Properties**（属性）选项卡。
   
   - 向下滚动到 **Event notifications**（事件通知），点击 **Create event notification**（创建事件通知）。
   
   - 在 **General configuration**（常规配置）中，输入 **Event name**（事件名称）为 `IMF_NOC_Event`。
   
   - 在 **Event Types**（事件类型）下，选中：
     - 所有对象创建事件
     
     - 所有对象删除事件
     
       ![image-20241021161223835](./img/image-20241021161223835.png)
     
   - 在 **Destination**（目的地）部分，选择 **SNS topic**，从下拉列表中选择 `topic_NOC_event`，然后保存更改。
   
6. **上传 NOC 文件**：
   - 页面重新加载后，点击 **Objects**（对象）选项卡，上传你的 `NOC_YYYYMMDD.TXT` 文件。
   - 成功上传或删除文件后，你应该会收到发送到你订阅地址的电子邮件通知。

如果你有任何其他问题或需要更多细节，请告诉我！