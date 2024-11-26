# 挂载efs

![image-20240810213122508](https://github.com/user-attachments/assets/e34dcaab-e97d-4520-b766-d63ef6c1e62b)


点击连接，选择了解跟多，查看官方教程

##### 环境配置命令：

```
yum install python3-pip -y
pip3 install botocore
yum install amazon-efs-utils -y 
```



##### 自动挂载助手

- 要在IAM授权的情况下自动挂载到具有EC2实例配置文件的 Amazon 实例，请在`/etc/fstab`文件中添加以下行。

  ```
  file-system-id:/ efs挂载点 efs _netdev,noresvport,tls,iam 0 0
  ```

file-system-id:

![image-20240810213814962](https://github.com/user-attachments/assets/3dcb844f-b8c1-4daa-8e39-4956581ca26d)


efs-mount-point : 本地路径

​			/mnt 

iam :启用iam验证

###### 放入配置文件：

```
vi  /etc/fstab
fs-049ca75898a96bdac:/ /mnt efs _netdev,noresvport,tls 0 0
```



##### 创建iam角色实现挂载

在iam服务中，选择角色，点击创建角色，选择ec2服务，点击下一步，点击下一步，点击创建

需要给iam用户关联这两个内联策略：

1. DescribeAvailabilityZones
2. DescribeMountTargets



