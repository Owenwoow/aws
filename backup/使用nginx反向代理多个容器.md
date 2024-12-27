## 1.安装ngnix

```shell
yum install nginx -y 
```



## 2.修改nginx.conf文件

要为 Nginx 配置缓存和基于最小连接数的负载均衡，你需要进行如下调整：

1. **缓存配置**：你可以使用 `proxy_cache` 指令在 Nginx 中配置缓存，将响应缓存到本地磁盘，从而减少后端服务器的负担。
2. **最小连接数负载均衡**：Nginx 默认的负载均衡策略是轮询。如果你想使用最小连接数（least_conn）策略，可以通过修改 `upstream` 块来实现。

### 更新后的 Nginx 配置文件

```nginx
worker_processes 1;  # 设置工作进程数

events {
    worker_connections 1024;  # 每个工作进程的最大连接数
}

http {
    # 定义缓存区域
    proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=app_cache:10m max_size=100m inactive=60m use_temp_path=off;

    upstream app_servers {
        # 使用最小连接数的负载均衡策略
        least_conn;
        
        server 127.0.0.1:8081;
        server 127.0.0.1:8082;
        server 127.0.0.1:8083;
        server 127.0.0.1:8084;
        server 127.0.0.1:8085;
        server 127.0.0.1:8086;
    }

    server {
        listen 80;  # Nginx 监听端口 80

        location / {
            proxy_pass http://app_servers;  # 将流量分发到 upstream

            # 配置缓存
            proxy_cache app_cache;
            proxy_cache_valid 200 60m;  # HTTP 200 状态码响应缓存 60 分钟
            proxy_cache_valid 404 1m;   # HTTP 404 状态码响应缓存 1 分钟
            proxy_cache_use_stale error timeout updating;  # 在发生错误时使用过期缓存

            # 设置转发的头部信息
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}
```

### 具体改动解释：

#### 1. **最小连接数负载均衡（least_conn）**

```nginx
upstream app_servers {
    least_conn;  # 使用最小连接数负载均衡
    server 127.0.0.1:8081;
    server 127.0.0.1:8082;
}
```

- `least_conn`：这个指令会让 Nginx 使用最小连接数的负载均衡策略。即，Nginx 会将请求发送到当前连接数最少的后端服务器上，从而避免某个服务器因为连接数过多而过载。
- 这在高并发场景下特别有用，可以确保负载均衡更为均匀。

#### 2. **缓存配置**

```nginx
# 配置缓存区域
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=app_cache:10m max_size=100m inactive=60m use_temp_path=off;
```

- ```
  proxy_cache_path
  ```

  ：这个指令用于设置缓存存储的路径和相关属性。它指定了缓存文件的存储位置和缓存的大小等参数。

  - `levels=1:2`：文件系统的存储层级，表示缓存文件存储在两层目录中。
  - `keys_zone=app_cache:10m`：指定一个缓存区 `app_cache`，并设置该缓存区的内存大小为 10MB。
  - `max_size=100m`：缓存最大空间为 100MB，超过这个空间的缓存会被删除。
  - `inactive=60m`：缓存未被访问的时间，如果超时则缓存失效。
  - `use_temp_path=off`：避免使用临时路径存储缓存。

#### 3. **缓存设置**

```nginx
proxy_cache app_cache;
proxy_cache_valid 200 60m;  # 200 状态码的响应缓存 60 分钟
proxy_cache_valid 404 1m;   # 404 状态码的响应缓存 1 分钟
proxy_cache_use_stale error timeout updating;  # 在发生错误时使用过期缓存
```

- `proxy_cache`：启用缓存，并指定缓存区 `app_cache`。

- ```
  proxy_cache_valid
  ```

  ：指定不同 HTTP 状态码的缓存时间。

  - 对于 200 状态码的响应，缓存 60 分钟。
  - 对于 404 状态码的响应，缓存 1 分钟。

- `proxy_cache_use_stale`：当发生错误（如超时）时，使用过期缓存继续提供服务，而不是直接返回错误。

#### 4. **其它常见的 `proxy_set_header` 配置**

```nginx
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

这些指令用于传递客户端请求的真实信息给后端服务器。

- `Host $host`：传递客户端请求的 Host 头部。
- `X-Real-IP $remote_addr`：传递客户端的真实 IP 地址。
- `X-Forwarded-For $proxy_add_x_forwarded_for`：传递经过代理的客户端 IP 地址链。

### 其他可能需要的配置项：

- **缓存清理**：如果你希望 Nginx 自动清理过期的缓存，可以通过调整 `inactive` 和 `max_size` 来设置合适的缓存大小和过期时间。
- **负载均衡的其他策略**：除了 `least_conn`，Nginx 还支持其他负载均衡策略，例如：
  - `round_robin`：轮询（默认方式）。
  - `ip_hash`：基于客户端 IP 地址进行负载均衡（用于保持会话粘性）。

### 重新加载配置

配置文件修改完成后，运行以下命令重新加载 Nginx 配置：

```bash
# 测试配置文件是否正确
nginx -t

# 重新加载 Nginx 配置
systemctl reload nginx
```

### 总结

1. **最小连接数负载均衡**：通过 `least_conn` 指令设置负载均衡策略为最小连接数。
2. **缓存配置**：使用 `proxy_cache` 和相关指令缓存 HTTP 响应，减轻后端负载并提高响应速度。
3. **过期缓存使用**：在发生错误时使用过期缓存，而不是直接返回错误。



以下是完整的 Nginx 配置代码，确保可以实现基于 IP 的会话保持，同时包含简洁的注释以便于理解。

**完整代码：**

```nginx
worker_processes auto;  # 根据 CPU 核心数自动调整工作进程数

events {
    worker_connections 1024;  # 每个进程的最大连接数（默认1024）
}

http {
    include       mime.types;         # 引入 MIME 类型配置
    default_type  application/octet-stream;

    # 优化 types_hash 的大小以解决 warning 提示
    types_hash_max_size 2048;
    types_hash_bucket_size 128;

    sendfile on;                      # 启用高效文件传输
    tcp_nopush on;                    # 提升发送大文件的效率
    tcp_nodelay on;                   # 减少 TCP 延迟

    keepalive_timeout 15;             # 连接的 keepalive 时间
    client_body_timeout 10;           # 请求体超时时间
    client_header_timeout 10;         # 请求头超时时间
    send_timeout 10;                  # 响应发送超时时间

    # 定义后端服务器的负载均衡组
    upstream backend {
        ip_hash;                      # 使用基于 IP 的会话保持策略
        server 127.0.0.1:8081;        # 后端服务器 1
        server 127.0.0.1:8082;        # 后端服务器 2
    }

    server {
        listen 80;                    # 监听 80 端口

        location / {
            proxy_pass http://backend;  # 代理请求到后端服务器

            # 设置代理头部，保留客户端信息
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            # 配置超时时间
            proxy_connect_timeout 30s;
            proxy_read_timeout 30s;
            proxy_send_timeout 30s;
        }
    }
}
```

------

### **命令总结**

1. **基本管理命令：**
   - 启动 Nginx: `sudo nginx`
   - 停止 Nginx: `sudo nginx -s stop`
   - 重载配置文件（无中断重启）：`sudo nginx -s reload`
   - 检查配置文件是否正确：`sudo nginx -t`
2. **配置文件路径：**
   - 默认配置文件路径：`/etc/nginx/nginx.conf`
   - 其他配置文件存放目录：`/etc/nginx/conf.d/`
3. **日志查看：**
   - 访问日志：`/var/log/nginx/access.log`
   - 错误日志：`/var/log/nginx/error.log`

------

### **之前遇到的问题与排错思路**

1. **问题 1:**

   - **提示**：`nginx: [warn] could not build optimal types_hash`

   - **原因**：`types_hash_max_size` 和 `types_hash_bucket_size` 参数过小。

   - 解决方法

     ：在配置文件 

     ```
     http
     ```

      段增加以下参数：

     ```nginx
     types_hash_max_size 2048;
     types_hash_bucket_size 128;
     ```

2. **问题 2:**

   - **提示**：`nginx: [emerg] no "events" section in configuration`

   - **原因**：配置文件中缺少 `events` 段。

   - 解决方法

     ：确保配置文件包含以下段落：

     ```nginx
     events {
         worker_connections 1024;
     }
     ```

3. **问题 3:**

   - **提示**：反向代理无法实现会话保持。

   - **原因**：未正确配置 `ip_hash`，导致每次请求都随机分配到后端服务器。

   - 解决方法

     ：在 

     ```
     upstream
     ```

      中添加 

     ```
     ip_hash
     ```

      参数以实现基于 IP 的会话保持：

     ```nginx
     upstream backend {
         ip_hash;
         server 127.0.0.1:8081;
         server 127.0.0.1:8082;
     }
     ```

4. **问题 4:**

   - **提示**：客户端收到 `Socket closed` 或请求连接失败。

   - 原因

     ：

     1. Nginx 的超时时间设置过短。
     2. 后端服务器响应缓慢。

   - 解决方法

     ：

     1. 调整 Nginx 的 

        ```
        proxy_read_timeout
        ```

         和 

        ```
        proxy_connect_timeout
        ```

        ：

        ```nginx
        proxy_connect_timeout 30s;
        proxy_read_timeout 30s;
        proxy_send_timeout 30s;
        ```

     2. 优化后端服务器性能或检查后端日志排查问题。

------

### **干净的配置代码：**

```nginx
worker_processes auto;

events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    types_hash_max_size 2048;
    types_hash_bucket_size 128;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;

    keepalive_timeout 15;
    client_body_timeout 10;
    client_header_timeout 10;
    send_timeout 10;

    upstream backend {
        ip_hash;
        server 127.0.0.1:8081;
        server 127.0.0.1:8082;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            proxy_connect_timeout 30s;
            proxy_read_timeout 30s;
            proxy_send_timeout 30s;
        }
    }
}
```

通过以上完整代码和总结，你可以快速部署并排查 Nginx 相关问题。



## 3.编写Docker部分

#### 安装docker&docker-compose

```shell
yum install docker -y 
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
cd /usr/local/bin
chmod +x docker-compose 
```



#### Dockerfile

```dockerfile
FROM public.ecr.aws/amazonlinux/amazonlinux:latest
RUN yum update -y && \
    mkdir /server
WORKDIR /server
COPY server /server
COPY server.ini /server
EXPOSE 80
ENTRYPOINT /server/server -port 80

```

#### 启动docker

```
docker run --rm -it -p 80:80 -v /root/.aws:/root/.aws root-server
docker run --rm -it -p 80:80 -v /mnt:/mnt root-images
```



#### docker-compose.yml

```yaml
version: '3.8'

services:
  app1:
    build:
      context: .
      dockerfile: Dockerfile  # 使用当前目录的 Dockerfile 来构建镜像
    container_name: app1
    ports:
      - "8001:80"  # 映射宿主机端口 8001 到容器 80 端口
    volumes:
      - /mnt:/mnt  # 将宿主机的 /mnt 目录挂载到容器的 /mnt
    networks:
      - app_network  # 使用 app_network 网络

  app2:
    build:
      context: .
      dockerfile: Dockerfile  # 使用当前目录的 Dockerfile 来构建镜像
    container_name: app2
    ports:
      - "8002:80"  # 映射宿主机端口 8002 到容器 80 端口
    volumes:
      - /mnt:/mnt  # 将宿主机的 /mnt 目录挂载到容器的 /mnt
    networks:
      - app_network  # 使用 app_network 网络

networks:
  app_network:
    driver: bridge  # 定义网络类型


```

直接使用镜像

```yaml
version: '3.8'
services:
  app1:
    image: app-images 
    container_name: app1
    ports:
      - "8081:80"
    volumes:
      - /mnt:/mnt
    networks:
      - app_network

  app2:
    image: app-images
    container_name: app2
    ports:
      - "8082:80"
    volumes:
      - /mnt:/mnt
    networks:
      - app_network

  app3:
    image: app-images
    container_name: app2
    ports:
      - "8083:80"
    volumes:
      - /mnt:/mnt
    networks:
      - app_network

networks:
  app_network:
    driver: bridge

```





