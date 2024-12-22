# 创建iam 分配和创建用户/角色的权限

## 题目：

### **背景**

你需要创建 IAM 角色，这个角色可用于开发进行测试。这个iam用户拥有分配和创建用户/角色的权限，但不超出公司安全策略定义的权限（最大权限`s3:*`是`ssm:PutParameter`）

### **你的任务** 

您的工作是创建一个委派管理员角色（角色 A）。

- 角色 A 将被开发人员用来为应用程序配置 IAM 资源。角色 A 仅在存在突出显示的最大权限时才可以创建 IAM 角色。
- 角色A必须被调用`SJ-DelegatedAdmin`，并且必须信任2个账户（你当前的账户和`681434714655`用于答案验证的账户）。
- 必须调用您的最大权限策略`SJ-Max`。
- **可选：**您可以测试从 SJ-DelegatedAdmin（角色 A）创建测试角色（角色 B），以确保您的解决方案正确无误。角色 B 应具有与角色 A 相同的信任关系



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

#### 权限一：将此 IAM 策略命名为SJ-Max

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



#### 权限二：将此策略命名为 SJ-Boundary

IAM 权限边界本身并不提供权限，而是限制总体权限的预防性控制。
您需要为委托 IAM 管理员创建权限策略才能创建角色。
[[为 AWS 服务创建角色](https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/id_roles_create_for-service.html)](https://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/id_roles_create_for-service.html)（官方文档）

创建策略：

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
                    "iam:PermissionsBoundary": "arn:aws:iam::{account_id}:policy/SJ-Max"
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
} 

```



#### 创建用户：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::12345:root",
          "arn:aws:iam::681434714655:root"
        ]
      },
      "Action": "sts:AssumeRole",
      "Condition": {}
    }
  ]
 }
ArthurMorgan
```

- 单击“更新策略”

