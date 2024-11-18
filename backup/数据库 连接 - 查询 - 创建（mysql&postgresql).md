## 从实例连接mysql

写入器url

![image-20241029150715962](https://github.com/user-attachments/assets/e0f8e4f3-407c-410d-8737-cc7b4ba2d9fc)




```
# 用yum查询mariadb
yum list | grep maridb

# 下载
yum inatall mariadb105


# 复制写入器url
# 登入数据库
mysql -u username -h url:\\ -p


# 查询数据库
show databases;


# 创建数据库
CREATE DATABASE 数据库名;


# 选择数据库
use suika;


```



## 连接postgresql


```
# 用yum查询mariadb
yum list | grep postgresql

# 下载
yum inatall postgresql16*


# 复制写入器url
# 登入数据库
psql -U hostname -h url:\\


# 查询数据库
\l


# 创建数据库
CREATE DATABASE 数据库名;


# 选择数据库
\c DBname

.
```
