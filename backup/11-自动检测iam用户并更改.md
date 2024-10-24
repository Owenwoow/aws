# 11-自动检测iam用户并更改

### 任务 1：构建 IAM 评估 Lambda 函数

第一项任务是构建一个 AWS Lambda 函数，以编程方式使用 IAM Access Analyzer 策略验证功能。

您可以使用 AWS IAM 访问分析器策略检查来验证您的策略。您可以使用 AWS CLI、AWS API 或 IAM 控制台中的 JSON 策略编辑器来创建或编辑策略。访问分析器会根据 IAM 策略语法和最佳实践来验证您的策略。您可以查看策略验证检查结果，其中包括安全警告、错误、一般警告和针对您的策略的建议。这些结果提供了可操作的建议，可帮助您编写实用且符合安全最佳实践的策略。

存货

您有一个 Lambda 函数`jam-validate-iam-policy-with-access-analyzer`。

你的任务

更新 AWS Lambda 函数`jam-validate-iam-policy-with-access-analyzer`以使用 IAM Access Analyzer 策略验证功能。

#### 解答：

1. 从**AWS 管理控制台**转到**AWS Lambda**控制台。
2. 选择功能`jam-validate-iam-policy-with-access-analyzer`。
3. 在**代码源**下，打开`index.py`文件。
4. 找到包含 < > 的行`to_be_completed`。
5. 将 < `to_be_completed`> 替换为`validate_policy`。
6. 选择**Deploy (部署)**保存您的更改。
7. （可选）在**代码源**下，选择**测试**。
8. （可选）在**事件名称**下，输入`triggerValidation`，不要更改内置`Hello-world`测试用例并用**创建**确认。
9. （可选）在**代码源**下，选择**测试**。
10. **（可选）等待 Lambda 函数执行完成。您应该在执行结果**选项卡中看到结果。





### 任务 2：向您的 Lambda 函数添加触发器

1. 从**AWS 管理控制台**转到**AWS Lambda**控制台。
2. 从登录页面选择`jam-validate-iam-policy-with-access-analyzer`。
3. **在配置**下，选择**触发器**。
4. **在触发器配置**下，选择**来源**为 Eventbridge **(Cloudwatch Events)**。
5. 选择**创建新规则**并填写详细信息。
6. 提供您选择的**规则名称**和**描述。**
7. 在**规则类型**下，选择**计划表达式**并提供输入`rate(1 day)`。
8. 单击屏幕底部的**添加按钮来创建触发器。**

### 任务 3：修复 IAM 访问分析器发现的问题

1. **按照线索 1**和**线索 2**中的步骤进行操作。

2. 更新`Resource`字段，将 ACCOUNT-ID 替换`*`为`arn:aws:iam::ACCOUTNT-ID:role/RoleName`举办 Jam 挑战的账户 ID，将 RoleName 替换为此账户中的给定 IAM 角色（可以是任何 IAM 角色）。目的是将`iam:PassRole`操作限制为一个或多个现有 IAM 角色。

3. 选择**“查看政策”**并通过**“保存更改”**进行确认。

   