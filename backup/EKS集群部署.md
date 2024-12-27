创建一个网络和相对应的子网

![image-20241210142318743](https://github.com/user-attachments/assets/d58bd030-927f-4391-9fc6-f613b8bfbbad)

## **创建集群**

##### **1.点击创建集群**

![image-20241210142448970](https://github.com/user-attachments/assets/436a9fee-0bc1-44cb-a4b4-ddc2b691d827)


##### **2.配置选项，自定义配置**

![image-20241210142544981](https://github.com/user-attachments/assets/223893f9-c4b4-4255-afa3-b2d0ed04043c)




##### **3.集群配置**

![image-20241210143337684](https://github.com/user-attachments/assets/9af64362-fa05-4715-b301-d0801c0a3de9)


##### **4.Kubernetes 版本设置**

![image-20241210150727195](https://github.com/user-attachments/assets/4a119a65-5712-41df-be0f-22a5d206cf84)


##### **5.剩下的选择默认点击下一步继续**

##### **6.设置联网**

![image-20241210155550872](https://github.com/user-attachments/assets/89d46c78-827a-436b-b6f1-0da0daf6ff0c)


##### **7.集群端点访问**

![image-20241210160259800](https://github.com/user-attachments/assets/48d4bb43-0092-4c07-a631-02b7bed4e70d)


##### 8.点击下一步选择**指标**和**控制面板日志记录**

![image-20241210155951972](https://github.com/user-attachments/assets/a29d7a23-717a-4625-966e-eda1f2cdef65)


##### 9.**选择插件**

![image-20241210160138075](https://github.com/user-attachments/assets/96c971cf-d81f-41f0-9355-bb57a6325c54)




## 创建redis

-----

![image-20241210170547880](https://github.com/user-attachments/assets/a6acbd61-0105-43e5-83da-c457d8df9fc9)


## 创建s3

-----

![image-20241211125107158](https://github.com/user-attachments/assets/bfc56843-7bbe-4cbb-9bb8-141ec6391d62)






## 连接和访问eks集群

-----

### 环境准备：

> 官方eks集群连接文档(https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/create-kubeconfig.html)

##### 1.更新aws 的授权凭证，使角色和创建eks集群的用户相同

Amazon EKS 使用带`kubectl`的`aws eks get-token`命令进行集群身份验证。默认情况下，AWS CLI 使用以下命令返回的相同原理：

```
aws sts get-caller-identity
```

输入后返回的值

![image-20241211131708094](https://github.com/user-attachments/assets/06fc1ae8-1a1a-47e2-98ee-06426d5df3bc)




##### 2.确保aws cli的版本为2

```bash
aws --version
```

![image-20241211132123706](https://github.com/user-attachments/assets/048fd556-700f-4b77-b45d-d29f9bd91bf0)






### 自动创建 `kubeconfig` 文件

##### 1.创建kubeconfig文件

为集群创建或更新**`kubeconfig`**文件。将**`region-code`**替换为您的集群所在的AWS区域，另外**`my-cluster`**替换为您的集群的名称。

```
aws eks update-kubeconfig --region region-code --name my-cluster
```

![image-20241211133150430](https://github.com/user-attachments/assets/dddb155f-e649-4026-88c1-881128e7357b)

##### 2.下载kubctl

```bash

[root@ip-10-0-1-234 ~]# curl -LO https://dl.k8s.io/release/v1.31.0/bin/linux/amd64/kubectl

# 服务文件可执行权限
[root@ip-10-0-1-234 ~]# chmod 755 kubectl 

# 移动到/bin目录下面
[root@ip-10-0-1-234 ~]# mv kubectl /bin

```

##### 3.测试配置

```
kubectl get svc
```

示例输出如下。

```
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
svc/kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   1m
```

##### 4.问解决

如果出现一直连接不上的情况的话可以尝试重新安装一下aws的cli

但是主要的问题还是去查看一下eks有没有附加上所需要的权限





## 创建EKS集群的计算节点

-----
![image-20241211143531647](https://github.com/user-attachments/assets/5044b45d-b4cb-4415-ac96-8f59ae217edd)


#### 第一步：配置节点组

![image-20241211144126819](https://github.com/user-attachments/assets/ca208d28-ad5d-4078-8af7-a4ff716f4e5a)


##### 其余的不动，点击下一步

![image-20241211144210650](https://github.com/user-attachments/assets/4b5b8dbe-c365-4fed-ae30-5b3bd3a06532)


#### 第二步：设置计算和扩展配置

##### 	**节点组计算和拓展配置：按照比赛要求选取**

![image-20241211144330554](https://github.com/user-attachments/assets/af00f88a-2507-4824-938d-981930746c65)


##### 	**节点组扩缩配置**：刚开始创建时选一个

![image-20241211144509897](https://github.com/user-attachments/assets/01fee975-6fd4-4f4a-97a5-20461931a68f)


##### 剩下配置不对，点击下一步

![image-20241211144605798](https://github.com/user-attachments/assets/d7f970f9-3558-4b31-a764-c57b57fc8b08)


#### 第三步：指定网络

![image-20241211144730514](https://github.com/user-attachments/assets/11809004-6c07-4ede-b499-0018eace73bf)


#### 第四步：创建

> 检查没有问题后创建节点





## 部署root服务和配置文件文件

-----

将文件传入服务并赋予服务可执行权限

```shell
# chmod 755 root 
# chmod 755 stub 
[root@ip-10-0-1-234 ec2-user]# ll
total 22080
-rwxr-xr-x. 1 ec2-user ec2-user 11096064 Dec 11 07:06 root
-rwxr-xr-x. 1 ec2-user ec2-user 11513856 Dec 11 07:06 stub
```

> 查看服务需要的参数 （放入appconfig)

```shell
[root@ip-10-0-1-234 ec2-user]# ./root -h
2eaa1af4-f8a8-4337-86c6-465068774055
Usage of ./root:
  -application string
        Application Name of AWS appConfig (default "Unicorn")
  -configProfile string
        Configuration profile of AWS appConfig (default "Unicorn")
  -environment string
        Environment Name of AWS appConfig (default "Production")
  -region string
        AWS Region where this app is running (default "us-east-1")

```



### Appconfig

##### 1.点击入门

![image-20241211151952888](https://github.com/user-attachments/assets/059e9834-e700-4858-bb07-22c0344fabdb)




##### 2.选择类型

![image-20241211152143673](https://github.com/user-attachments/assets/5d4352b4-d120-4f6b-8b9e-2f6374c492c4)


##### 3.定义配置

![image-20241211191443888](https://github.com/user-attachments/assets/a7f453d2-3b98-41a0-88ab-ffe359e1c06e)


```shell
{ 
"RedisHost": "redis1.yfwyr3.ng.0001.use1.cache.amazonaws.com", 
"RedisPort": "6379", 
"FsPath": "/mnt/", 
"Port": 80, 
"Bucket": "unicorn-us-es-ws-1211" 
}
```



##### 4.定义应用程序名称

![image-20241211191552748](https://github.com/user-attachments/assets/fe3e09ef-9388-4ded-9e7f-ea4778d3160e)


##### 5.创建环境

![image-20241211191718663](https://github.com/user-attachments/assets/293e45c4-3043-47e0-bfbb-db8e5c74c25f)


##### 6.开始部署

![image-20241211191842302](https://github.com/user-attachments/assets/70bc3178-ab7e-4958-8815-319f873c0d62)

#### 连接Appconfig，并把服务润起来

> 配置参数
>
> Usage of ./root:
>   -application string
>         Application Name of AWS appConfig (default "Unicorn")
>   -configProfile string
>         Configuration profile of AWS appConfig (default "Unicorn")
>   -environment string
>         Environment Name of AWS appConfig (default "Production")
>   -region string
>         AWS Region where this app is running (default "us-east-1")

```
./root -application root-server -configProfile root-config -environment dev -region us-east-1
```

![image-20241211195236609](https://github.com/user-attachments/assets/5065d6f9-9cd4-4a53-bec3-de56d2924fdb)




### 构建镜像

> eks服务其实是一个k8s集群，k8s集群是负责管理容器的，那我们就先要构建一个镜像

##### 1.编写Dokcerfile

```shell
# 创建目录
[root@ip-10-0-1-234 ec2-user]# mkdir root-server 
[root@ip-10-0-1-234 ec2-user]# mv root root-server/
[root@ip-10-0-1-234 ec2-user]# ls
root-server  stub
[root@ip-10-0-1-234 ec2-user]# cd root-server/

# 安装docker 
yum install docker -y
systemctl start docker 
systemctl enable docker

# Dokcerfile
vi Dockerfile
FROM public.ecr.aws/amazonlinux/amazonlinux:latest
RUN yum update -y && \
    mkdir /app
WORKDIR /app
COPY root /app/root
EXPOSE 80
ENTRYPOINT /app/root -application root-server -configProfile root-config -environment dev -region us-east-1

# 创建images
docker build -t root-server .

# 检查
[root@ip-10-0-1-234 root-server]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
root-server   latest    c63c284dc803   14 seconds ago   225MB
```

##### 2.启动容器

> 运行容器的时候可能需要对应的权限，我们可以将 **/root/.aws** 下的 **config**  **credentials** 文件映射到容器中

```shell
docker run --rm -it -p 80:80 -v /root/.aws:/root/.aws root-server
```

##### 3.将容器推送到ECR上去

![image-20241211204406738](https://github.com/user-attachments/assets/fb23c0fd-c71f-4d42-a3f1-bfdbf2f0a781)


```shell
# 推送镜像的命令
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211125438445.dkr.ecr.us-east-1.amazonaws.com
docker tag root-server:latest 211125438445.dkr.ecr.us-east-1.amazonaws.com/root-server:latest
docker push 211125438445.dkr.ecr.us-east-1.amazonaws.com/root-server:latest
```

记录视频1.24：44



https://docs.aws.amazon.com/eks/latest/userguide/managing-auth.html

去访问文档中的这个项目：[[使用 kubectl 访问集群](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/create-kubeconfig.html)](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/create-kubeconfig.html)

```bash
aws sts get-caller-identity
```

查看ec2的登入信息

![image-20240830224642429](https://github.com/user-attachments/assets/44a14fbb-b5e0-4635-bb1b-9064a09fb300)

以为权限的问题我把不能查看信息，所有我能可以使用之前创建的teamrole用户登入

```
[root@ip-10-0-1-251 ~]# aws configure 
AWS Access Key ID [None]: AKIATCKAOU3HLEUAHCY5 
AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1
Default output format [None]: 
```

登入上后我们再去查看AWS的授权凭证

![image-20240830224929925](https://github.com/user-attachments/assets/a04cc99b-963d-4e72-bdb1-c0c6312084bb)


可以了，接下来

**使用 AWS CLI 创建** `kubeconfig`

```bash
aws eks update-kubeconfig --region us-east-1 --name cluster
```

![image-20240830231730922](https://github.com/user-attachments/assets/e18fe99b-8ecd-401f-b08c-4f230266d4fb)


查看一下他给的地址

![image-20240830231932726](https://github.com/user-attachments/assets/eb9702c1-b4cd-459c-818a-8320ebfccb29)


测试配置

```bash
kubectl get svc
```

示例输出如下。

```
NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
svc/kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   1m
```

如果您收到任何授权或资源类型错误，请参阅故障排除主题中的 [[未经授权或访问被拒绝 (kubectl)](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/troubleshooting.html#unauthorized)](https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/troubleshooting.html#unauthorized)。

![image-20240830234323795](https://github.com/user-attachments/assets/e5e88cc5-1ae7-40da-a69e-8d4185e56d66)


如果无法访问需要去更改一下iam用户的权限



```bash
kubectl get node
```

查看计算服务

![image-20240830234504864](https://github.com/user-attachments/assets/6c6ad965-2ea6-4f26-a94e-ad597436ac85)


现在是没有的状态我们去aws控制太中创建一下

![image-20240830234637741](https://github.com/user-attachments/assets/9ef8c1d4-571b-46c7-91ce-8e9bb742bb18)


![image-20240830235329648](https://github.com/user-attachments/assets/b84f89a6-5bfd-4411-a38a-a5aa80b793e8)


下一步，在点击下一步

![image-20240830235504950](https://github.com/user-attachments/assets/0929985d-3de4-4a7b-81be-1b7aa50f150c)













### 二.配置服务



#### 1.创建config

将配置文件导入后启动，发现他需要四个服务

 
![image-20240831113457760](https://github.com/user-attachments/assets/9ad33307-2e17-4944-aef5-735547ec4728)



在aws控制台中搜素appconfig，从文档中找去一段更服务的json配置文件复制进去

 
![image-20240831114136920](https://github.com/user-attachments/assets/e3ad5e81-b46c-40cd-b8ae-922d1d21e7f5)



点击下一步，应用服务名称设为`root-server`

 
![image-20240831114240610](https://github.com/user-attachments/assets/5c5032be-fbfd-4e89-aa6c-389c51c46dc1)



保存并继续部署

 


#### 2.运行服务

在程序上运行

![image-20240831121827930](https://github.com/user-attachments/assets/57db480d-def2-4652-b394-6a751760726b)

连接成功，但是我们在本地是跑不起来的，我们需要构架一个docker镜像



#### 3.创建docker镜像

先将文件整理一下，保存到一个目录

![image-20240831122536774](https://github.com/user-attachments/assets/b70f2110-3cec-4673-b938-ec4f8db26ffa)


安装docker并设置自启动

```bash
yum install docker
systemctl start docker
systemctl enable docker
systemctl status docker
```



创建dockerfile文件

```bash
vi Dockerflie
----------------------------------------------------
FROM centos
COPY server /
ENTRYPOINT /server -application server-root -configProfile config -environment dev -region us-east-1 
----------------------------------------------------
```

创建镜像

```bash
docker build -t server:root .
docker images
```



#### 4.运行docker

![image-20240831132805731](https://github.com/user-attachments/assets/afbff937-d8fd-4c27-ad7a-c7c4c448da30)


发现无法获得检索凭证，所以我们要让他主动获取凭证`/root/.aws`

```bash
docker run --rm -it -v /root/.aws:/root/.aws server:root
```

- **`docker run`**：该命令用于从Docker 镜像创建并启动容器。

  **`--rm`**：此标志告诉 Docker 在退出时自动删除容器。通过删除不再需要的容器，它有助于保持系统清洁。

  **`-it`**：这是两个标志的组合：

  - `-i`（交互式）：保持 STDIN 开放，允许您与容器交互。
  - `-t`(tty)：分配一个伪TTY，提供终端接口。

  **`-v /root/.aws:/root/.aws`**：此标志将卷从主机安装到容器中。

  - `/root/.aws`（冒号之前）是主机上的路径。
  - `/root/.aws`（冒号后面）是容器内的路径。
  - 此挂载允许容器从主机访问 AWS 配置文件，如果您的应用程序需要 AWS 凭证或配置，这将很有用。

  **`server:root`**：指定用于容器的 Docker 镜像。在本例中，它是带有标签`server`的镜像`root`。


![image-20240831133800575](https://github.com/user-attachments/assets/041e80ad-1084-4b6f-ab5b-9424fff5b76b)




#### 5.上传镜像

把镜像放到ecr上，在ecr上创建私有存储库

![image-20240831173033871](https://github.com/user-attachments/assets/a76de791-fa89-4d4b-9fdc-104e59332a08)


点击查看存储库复制命令

![image-20240831173129035](https://github.com/user-attachments/assets/08e2b422-6d75-45cc-a2cd-b13cab52bce7)


最后得到这个表示上传成功





### 三.在k8s运行镜像

k8s是一个调度系统我们需要去写一个配置文件去运行镜像，我们这次选择`deploy`进行调度

#### 1.上传镜像到erc

先创建一个k8s的目录用于存放文件

```
mkdir k8s
```

将yaml文件写入`deploy-root.yaml`

```bash
vi deploy-root.yaml
--------------------------------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-root
spec:
  replicas: 1
  selector:
    matchLabels:
      app: root
  template:
    metadata:
      labels:
        app: root
    spec:
      containers:
      - name: server-root
        image: 211125446350.dkr.ecr.us-east-1.amazonaws.com/server:root
------------------------------------------------------------
```

保存完后运行`kubectl apply -f deplooy-root.yaml`

![image-20240831173813072](https://github.com/user-attachments/assets/bed84578-05bb-4d5d-8b2f-7745ccf4a031)


可以看见kubectl给我们创建了一个deployment，我们可以使用这个命令进行检查

```bash
kubectl get deploy
```

![image-20240831174236871](https://github.com/user-attachments/assets/aa789bfd-3645-4a6e-8507-3791b5d3559d)


确实已经创建成功，那么具体他创建的容器叫上面呢

```bash
kubectl get pod
```

![image-20240831174357858](https://github.com/user-attachments/assets/d6b2f07e-941a-4f83-bc4b-d053949cd66c)


但是它显示不能运行，查看一下他的日志

```bash
kubectl logs deploy-root-7cc959688c-8csfd
```

![image-20240831174523757](https://github.com/user-attachments/assets/08e252a0-8a87-4df0-87bb-f8acd49daee9)


发现没有对应权限，我们重新看会文档

![image-20240831174615463](https://github.com/user-attachments/assets/72e16951-42e5-48c3-8385-4b3994b7a487)




#### 2.为 Kubernetes 服务账户分配 IAM 角色

需要创建unicorn角色

![image-20240831174743711](https://github.com/user-attachments/assets/c29be020-cad0-4816-91ad-efc93990a6a7)

https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/iam-roles-for-service-accounts.html

视频教程 `1:34:29/3:24:23`

创建 IAM 角色并将其与 Kubernetes 服务账户关联。

创建OIDC

![image-20240831155849501](https://github.com/user-attachments/assets/53bdc9fd-ff1e-4561-8541-c38fb1aeb7ae)


1. 如果您有要分派 IAM 角色的现有 Kubernetes 服务账户，则您可以跳过此步骤。

   创建 Kubernetes 服务账户。将以下内容复制到您的设备。将 `my-service-account` 替换为所需的名称，如有必要，将 `default` 替换为其他命名空间。如更改 `default`，则命名空间必须已经存在。

   ```
   cat >my-service-account.yaml <<EOF
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: sa1
     namespace: default
   EOF
   kubectl apply -f my-service-account.yaml
   ```

![image-20240831185458083](https://github.com/user-attachments/assets/52cc41e7-8a3b-4655-87e8-0ff50dd29a83)


2.创建角色

确认角色和服务账户配置正确。

1. 确认 IAM 角色的信任策略配置正确。

   ```bash
   aws iam get-role UnicornRole my-role --query Role.AssumeRolePolicyDocument
   ```

   示例输出如下。

   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "Federated": "arn:aws:iam::111122223333:oidc-provider/oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE"
               },
               "Action": "sts:AssumeRoleWithWebIdentity",
               "Condition": {
                   "StringEquals": {
                       "oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE:sub": "system:serviceaccount:default:my-service-account",
                       "oidc.eks.region-code.amazonaws.com/id/EXAMPLED539D4633E53DE1B71EXAMPLE:aud": "sts.amazonaws.com"
                   }
               }
           }
       ]
   }
   ```

绑定用户

 `kubectl edit sa sa1`

![image-20240901142738024](https://github.com/user-attachments/assets/a51caf01-05fb-45ba-b8a2-9d5a85c405ee)


更改这个iam用户也可以



#### 3.配置 Pods 以使用 Kubernetes 服务账户

https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/pod-configuration.html

改完配置文件后，重新上传一下配置

```
kubectl apply -f my-deployment.yaml
```

![image-20240831185543214](https://github.com/user-attachments/assets/a7b8856b-1ab0-447d-b3b9-6f473e3199ea)






### 四.连接eks集群

应为我们的eks集群是在内外中的我们没法访问，需要使用其他的方法

![image-20240831190019085](https://github.com/user-attachments/assets/7b023956-a1b6-41e5-87aa-015aec2f5d46)


详信息可以看这一片文档

#### 1.创建iam角色

创建ELBControllerRole角色



#### 2.创建yaml配置文本

1. 将以下内容复制到您的设备。请将 `111122223333` 替换为您的账户 ID。如果您的集群位于 AWS GovCloud（美国东部）或 AWS GovCloud（美国西部）AWS 区域，则将 `arn:aws:` 替换为 `arn:aws-us-gov:`。替换文本后，运行修改后的命令可创建 `aws-load-balancer-controller-service-account.yaml` 文件。

```
cat >aws-load-balancer-controller-service-account.yaml <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/name: aws-load-balancer-controller
  name: aws-load-balancer-controller
  namespace: kube-system
  annotations:
    eks.amazonaws.com/role-arn: 
    arn:aws:iam::211125446350:role/ELBControllerRole
EOF
```

运行一下

![image-20240831192808054](./img/image-20240831192808054.png)



#### 3.使用 Helm 安装 AWS Load Balancer Controller

1.安装helm

```bash
wget https://get.helm.sh/helm-v3.15.4-linux-amd64.tar.gz
tar -zxvf helm-v3.15.4-linux-amd64.tar.gz 
```

```
[root@ip-10-0-1-251 k8s]# ls
aws-load-balancer-controller-service-account.yaml  deploy-root.yaml  helm-v3.15.4-linux-amd64.tar.gz  linux-amd64  my-service-account.yaml
[root@ip-10-0-1-251 k8s]# cd linux-amd64/
[root@ip-10-0-1-251 linux-amd64]# ls
LICENSE  README.md  helm
[root@ip-10-0-1-251 linux-amd64]# 
[root@ip-10-0-1-251 linux-amd64]# cp helm /bin
[root@ip-10-0-1-251 linux-amd64]# 
[root@ip-10-0-1-251 linux-amd64]# helm
The Kubernetes package manager

Common actions for Helm:

```



2.安装 AWS Load Balancer Controller

###### 使用 [[Helm V3](https://helm.sh/)](https://helm.sh/) 安装 AWS Load Balancer Controller

1. 添加 `eks-charts` Helm 图表存储库。AWS 在 GitHub 上维护[[此存储库](https://github.com/aws/eks-charts)](https://github.com/aws/eks-charts)。

   ```
   helm repo add eks https://aws.github.io/eks-charts
   ```

2. 更新您的本地存储库，以确保您拥有最新的图表。

   ```
   helm repo update eks
   ```

3. 安装 AWS Load Balancer Controller。

   将 `my-cluster` 替换为您的集群名称。在以下命令中，`aws-load-balancer-controller` 是您在上一步中创建的 Kubernetes 服务账户。

   有关配置 Helm 图表的更多信息，请参阅 GitHub 上的 [`[values.yaml](https://github.com/aws/eks-charts/blob/master/stable/aws-load-balancer-controller/values.yaml)`](https://github.com/aws/eks-charts/blob/master/stable/aws-load-balancer-controller/values.yaml)。

   ```
   helm u'ninstall aws-load-balancer-controller eks/aws-load-balancer-controller \
     -n kube-system \
     --set clusterName=cluster \
     --set serviceAccount.create=false \
     --set serviceAccount.name=aws-load-balancer-controller 
   ```

   
![image-20240831194819459](https://github.com/user-attachments/assets/371e2b2c-5db8-4f25-9f45-9104233952a2)


创建成功

​	4.检查是否在运行

```
kubectl get pod -A
```

![image-20240831194942035](https://github.com/user-attachments/assets/e2b64c98-de95-4f21-ba8b-a99fbe012df3)


能看到他已经在运行了









![image-20240831114357663](https://github.com/user-attachments/assets/a56ec70d-60aa-46db-85b5-cabd6f1b3e32)
