# Wordpress 密码是什么？

------

## 概述

当您收到紧急问题报告时，您是一家繁忙的数字代理机构中熟练的 Web 开发人员和 Amazon Lightsail 的权威。现有的 WordPress 网站必须立即迁移到 Amazon Lightsail。服务器上输入错误的命令导致原始网站被意外清除，从而使其容易受到攻击。

当您进一步进行恢复和迁移过程时，会发现原始服务器提供给您的备份文件。这个备份文件确实是 WordPress 备份，但它不包含完成迁移所需的数据库和管理员密码。

您必须使用备份文件将网站成功迁移到 Amazon Lightsail，然后在时间耗尽之前通过发现不同的方法仅重置管理员用户的密码。

------

### 任务 1：您是否设法通过提供的备份迁移了 WordPress 网站？

#### 背景

迁移活动完成后，将为您提供一个来自原始服务器的名为 `backup-49b2.wpress` 的备份文件和一个运行 WordPress 博客平台的名为 `Migrated-Website` 的 Amazon Lightsail 实例。

#### 任务要求

- 访问 AWS 控制台
- 通过 SSH 协议连接到 Amazon Lightsail 实例
- 检索 WordPress 管理仪表板的凭据
- 访问 WordPress 管理仪表板
- 将 WordPress 网站迁移到名为 `Migrated-Website` 的 Amazon Lightsail 实例。

#### 入门步骤

1. 访问 AWS 控制台。
2. 通过 SSH 协议连接到 Amazon Lightsail 实例。
3. 检索 WordPress 管理仪表板的凭据。
4. 访问 WordPress 管理仪表板。
5. 迁移 WordPress 网站。

#### 线索

- **线索 1：如何开始以及提供的实际备份文件是什么？**

  WordPress 网站可以通过多种不同的方式迁移，其中最常见的方法是使用 WordPress 插件。
   提供的备份文件以非常具体的扩展名结尾 `.wpress`。此扩展名是保留的，特定于非常常见的 WordPress 迁移插件。

  - **解锁线索**：
     检查哪个 WordPress 插件将导出具有指定文件扩展名的备份。



- **线索 2：找到备份文件并完成迁移。**

  **步骤 A**：识别 WordPress 插件

  获得的备份文件以扩展名结尾 `.wpress`，这意味着它是通过一个非常具体的 WordPress 插件导出的。用于导出网站的插件是 **All-in-One WP Migration and Backup**。

  **步骤 B**：下载 WordPress 备份文件

  - 单击此挑战中的“输出属性”选项卡，以检索下载名为 `backup-49b2.wpress` 的 WordPress 备份文件的直接 URL。
     属性名称为 `WordPressBackupFile`。

  **步骤 C**：访问 Amazon Lightsail 实例的 WordPress 管理仪表板

  - 要检索默认管理员用户的凭据，我们应该通过 SSH 协议访问 Amazon Lightsail 实例。

  - AWS Console开始挑战后，单击按钮即可访问 AWS 控制台。

  - 访问后，单击此挑战中的“输出属性”选项卡以检索通过 SSH 访问 Amazon Lightsail 实例的直接 URL。
     单击显示的相应 URL，这将打开一个带有 SSH 终端的新浏览器窗口。属性名称为 `LightsailNewInstanceSSHAccessURL`。

  - 在 SSH shell 上执行以下命令：

    ```bash
    cat bitnami_application_password
    ```

  - 复制从上一个命令中检索到的凭证。复制后，返回此挑战的输出属性，单击名为 `WordpressAdminPanel` 的属性显示的 URL。
     这是 Amazon Lightsail 实例上的 WordPress 管理面板的直接访问 URL。

  - 在页面上输入凭证，用户名 `user` 和密码是通过执行上面的命令检索到的。



------

### 任务 2：但是密码是什么？

#### 背景

您已成功利用备份文件迁移到 Amazon Lightsail 实例。但是，完成迁移的最后阶段所需的 WordPress 管理员凭据未知。有关管理员凭据的唯一已知信息是用户名 `user`。

#### 任务要求

- 访问 AWS 控制台
- 通过 SSH 协议连接到 Amazon Lightsail 实例
- 重置 WordPress 用户帐户凭据
- 使用新凭据访问 WordPress 管理仪表板

#### 入门步骤

1. 访问 AWS 控制台。
2. 通过 SSH 协议连接到 Amazon Lightsail 实例。
3. 重置 WordPress 用户帐户凭据。
4. 使用新凭据访问 WordPress 管理仪表板。

------

### 线索

#### 线索 1：我们应该尝试连接数据库吗？

- 根据初始迁移活动，只有管理员凭据未知。但是，Amazon Lightsail 实例上的迁移网站可以正常运行并与底层数据库进行通信。
- 检查并发现检索数据库密码和重置用户凭证的方法。

#### 线索 2：让我们重置凭证。

##### 解决方案 1

1. 检索 `bn_wordpress` 用户密码以便能够连接数据库。密码存储在 `/opt/bitnami/wordpress/wp-config.php` 文件中。执行以下命令：

   ```bash
   sudo cat /opt/bitnami/wordpress/wp-config.php | grep DB_PASSWORD
   ```

2. 从输出中复制并存储密码。

- **目的**：从 `wp-config.php` 配置文件中提取数据库的密码。`wp-config.php` 是 WordPress 安装目录下的一个配置文件，包含了连接数据库所需的各种信息，包括数据库用户名 (`DB_USER`)、数据库密码 (`DB_PASSWORD`)、数据库名称 (`DB_NAME`) 等。

- **原理**：该命令使用 `grep` 搜索文件中的 `DB_PASSWORD`，并返回与数据库密码相关的行。密码会存储在单引号之间，因此可以从返回的输出中提取密码。

  

​    3.更新管理员用户 `user` 的密码：

```bash
sudo mysql -u bn_wordpress -p bitnami_wordpress -e "UPDATE wp_users SET user_pass=MD5('51yH!29xgT') WHERE ID=1;"
```

注意：输入密码时，密码不可见。

- **目的**：直接通过 MySQL 命令行工具更新 WordPress 用户的密码，而不需要进入 MariaDB shell。

- 原理

  ：

  - `sudo mysql -u bn_wordpress -p bitnami_wordpress`：通过 `mysql` 命令连接到数据库。`-u bn_wordpress` 传递了连接数据库所用的用户名，`-p` 之后会提示输入密码，`bitnami_wordpress` 是要连接的数据库名称。
  - `-e "SQL语句"`：允许直接在命令行中执行 SQL 查询，而无需进入交互式 MySQL shell。这个查询更新 `wp_users` 表中的 `user_pass` 字段。
  - `UPDATE wp_users SET user_pass=MD5('51yH\!29xgT') WHERE ID=1`：更新 ID 为 1 的用户的密码。WordPress 中的管理员用户通常是 ID 为 1。`user_pass` 字段保存的是加密后的密码，使用 `MD5()` 函数对新密码 `51yH!29xgT` 进行加密。**注意**：MD5 是一种已被认为不安全的哈希算法，当前 WordPress 已经改用更安全的加密算法，但这里仍然是为了演示如何通过 SQL 更新密码。
  - 执行此命令时，系统会提示输入从步骤 1 获取的数据库密码。



​    4.确认密码是否已重置：

访问 [[WordPress 管理员面板](http://public-ip/wp-admin/)](http://public-ip/wp-admin/)，并使用以下新凭据登录：

- 用户名: `user`
- 密码: `51yH!29xgT`

### SQL 命令分解

1. **`mysql -u bn_wordpress -p bitnami_wordpress`**：通过 MySQL 连接到数据库，使用 `bn_wordpress` 作为用户名，`bitnami_wordpress` 作为数据库名。
2. **`-e "SQL语句"`**：执行 SQL 语句，而不进入 MySQL 交互模式。
3. **`UPDATE wp_users SET user_pass=MD5('51yH!29xgT') WHERE ID=1`**：更新管理员（ID 为 1）的密码，使用 MD5 对新密码进行加密。



##### 解决方案 2

1. 通过 SSH 访问 Amazon Lightsail 实例，并下载名为 `reset.php` 的 PHP 脚本：

   ```bash
   sudo curl https://aws-jam-challenge-resources-**region**.s3.amazonaws.com/wp-migration-blunders/reset.php.txt -o /opt/bitnami/wordpress/reset.php
   ```

2. 设置文件权限：

   ```bash
   sudo chmod 775 /opt/bitnami/wordpress/reset.php && sudo chown bitnami:daemon /opt/bitnami/wordpress/reset.php
   ```

3. 访问重置 URL 来重置管理员密码：

   ```bash
   http://public-ip/reset.php?pass=51yH!29xgT
   ```

4. 确认密码是否已重置：

   访问 [[WordPress 管理员面板](http://public-ip/wp-admin/)](http://public-ip/wp-admin/)，并使用以下新凭据登录：

   - 用户名: `user`
   - 密码: `51yH!29xgT`