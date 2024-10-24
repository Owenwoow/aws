# 委派 IAM 管理员

## 题目：

### **背景**

您是一家中型企业的云安全管理员。目前，所有 IAM 用户和 IAM 角色创建都必须通过您的团队，这已成为贵公司的瓶颈。开发人员需要更高的灵活性，并且需要权限来创建 IAM 角色，这些角色将由他们的应用程序承担。这将允许开发人员构建和测试他们的应用程序并快速创新。

您的团队主管希望为开发人员提供创建 IAM 用户的权限。他们担心开发人员将分配给新用户/角色的权限，也担心权限提升。

您的主管已指派您一项任务，让您想出一个解决方案，为开发人员分配创建用户/角色的权限，但这些角色不应超出公司安全策略定义的权限。您的挑战是创建一个系统，您可以委派开发人员创建自己的 IAM 角色，以加快开发周期，同时保持安全性。开发人员应具有的最大权限`s3:*`是`ssm:PutParameter`

### **你的任务** 

您的工作是创建一个委派管理员角色（角色 A）。

- 角色 A 将被开发人员用来为应用程序配置 IAM 资源。角色 A 仅在存在突出显示的最大权限时才可以创建 IAM 角色。
- 角色A必须被调用`SJ-DelegatedAdmin`，并且必须信任2个账户（你当前的账户和`681434714655`用于答案验证的账户）。
- 必须调用您的最大权限策略`SJ-Max`。
- **可选：**您可以测试从 SJ-DelegatedAdmin（角色 A）创建测试角色（角色 B），以确保您的解决方案正确无误。角色 B 应具有与角色 A 相同的信任关系

### **您应该使用的服务**

- AWS IAM

### **任务验证**

**验证解决方案的步骤**

- 运行以下命令替换`12345`AWS 帐号

  - Mac 或 Linux 机器：

    ```
    curl -d '{"account_id":"12345"}' -H "Content-Type: application/json" -X POST https://1b07hvp92b.execute-api.us-east-1.amazonaws.com/prod/validate
    ```

  - 窗户：

    ```
    curl -d "{\"account_id\":\"12345\"}" -H "Content-Type: application/json" -X POST https://1b07hvp92b.execute-api.us-east-1.amazonaws.com/prod/validate
    ```

- 将运行 curl 命令后获得的答案文本输入到`Enter answer here`JAM 控制台的字段中





## 答案：

需要创建两权限附加到用户上

权限一：

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "ssm:PutParameter"
            ],
            "Resource": "*"
        }
    ]
}
```



权限二：

IAM 权限边界本身并不提供权限，而是限制总体权限的预防性控制。
您需要为委托 IAM 管理员创建权限策略才能创建角色。
步骤
创建具有创建 IAM 角色权限的新 IAM 策略将由委派管理员使用。
分配权限边界以确保只有在存在最大权限边界时才能创建该角色。

1。前往 IAM 控制台 2。在左侧窗格中单击 “策略”，然后单击 “创建策略” 按钮。 3。复制下方的 JSON 并将其放入 JSON 选项卡中 4。将此政策命名为 SJ-Boundary 5。在 IAM 条件下，将 12345 更改为你的账号。

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "RoleCreationWithPermission",
            "Effect": "Allow",
            "Action": [
                "iam:DetachRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:CreateRole",
                "iam:AttachRolePolicy",
                "iam:PutRolePolicy"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "iam:PermissionsBoundary": "arn:aws:iam::12345:policy/SJ-Max"
                }
            }
        },
        {
            "Sid": "IAMPermission",
            "Effect": "Allow",
            "Action": [
                "iam:CreatePolicy",
                "iam:GetRole",
                "iam:ListRoles",
                "iam:DeletePolicy",
                "iam:DeleteRole",
                "iam:GetRolePolicy"
            ],
            "Resource": "*"
        }
    ]
} 继续使用此策略为您的委派管理员创建角色。
```


