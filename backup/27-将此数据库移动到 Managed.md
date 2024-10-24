# **将此数据库移动到 Managed**

![image-20241021183830010](./img/image-20241021183830010.png)

### **将此数据库移动到 Managed**

![image-20241021184550852](./img/image-20241021184550852.png)

线索 1：提示



您需要创建一个新的 RDS 选项组并将其应用于 RDS 实例。



线索 2：文档


按照[此处](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.SQLServer.Options.BackupRestore.html)的说明创建具有正确设置的选项组，以便使用 Amazon S3 进行备份和还原


线索 3：步步维艰


导航到 **RDS** Service 控制台单击菜单中的 **Option groups**单击 **Create group （创建组）**为 Name （名称） 和 Description （描述） 输入任何值为 *Engine* 选择 **sql-server-se**选择 **15.00** 作为*主要引擎版本*，然后单击 **Create**打开您刚刚创建的群组单击 **Add Option**选择“SQLSERVER_BACKUP_RESTORE”作为选项，然后从任务清单中选择 IAM 角色在 *Scheduling* and **Add Option** 下选择 **Immediately**单击**菜单中的 Databases**。从任务清单中选择 RDS 实例，然后单击 **Modify**将 **Option Group （选项组**） 更改为您刚刚创建的组。单击 **Continue**对于 Schedule modifications（计划修改），选择 **Apply Immediately（立即应用**）。单击 **Modify DB instance（修改数据库实例**）

![image-20241021190335965](./img/image-20241021190335965.png)

![image-20241021190350252](./img/image-20241021190350252.png)