## Docker容器

### 单独的jar包在Docker种的部署方式

1. 构建好的jar包、Dockerfile文件以及resources、lib各种依赖文件上传至服务器某目录（例：`/data/docker/demo`）下

2. 编译Dockerfile文件`docker build -t image_name .`

3. 根据镜像创建容器，并指定端口映射`docker run -d --name container_name -p 8080:8080 image_name`

4. 查看容器是否创建并运行成功`docker ps`

5. 查看容器日志`docker logs container_id`

### 基础操作命令

列出容器
```
docker ps [OPTIONS]
```
参数说明：  
[-a] &nbsp;&nbsp; 显示全部容器，包括未运行的  
[-q] &nbsp;&nbsp; 只显示容器编号  
[-n] &nbsp;&nbsp; 列出最新创建的n个容器

删除容器
```
docker rm [OPTIONS] container
```
参数说明：  
[-f] &nbsp;&nbsp; 强制删除一个正在运行的容器    
[-l] &nbsp;&nbsp; 容器间的网络连接，而非容器本身  
[-v] &nbsp;&nbsp; 删除与容器关联的卷

查看容器运行的进程信息
```
docker top container
```

查看容器运行日志
```
docker logs [OPTIONS] container
```
参数说明：  
[-f] &nbsp;&nbsp; 跟踪最新日志输出  
[-t] &nbsp;&nbsp; 显示时间戳  
[--tail] &nbsp;&nbsp; 后面加上数字n表示列出n条日志信息  

列出镜像
```
docker images [OPTIONS]
```
参数说明：  
[-a] &nbsp;&nbsp; 列出所有镜像  
[-q] &nbsp;&nbsp; 只显示镜像ID  


获取容器/镜像的元数据
```
docker inspect name|id
```

批量删除tag为<none>的镜像
```
docker images|grep none|awk ‘{print $3}’|xargs docker rmi
```
或者
```
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

获取容器内root权限

```shell
docker exec -it -u root container_id bash
```

### Dockerfile文件

```text
FROM：指定基础镜像，必须为第一个命令

MAINTAINER: 维护者信息

LABEL：用于为镜像添加元数据

WORKDIR：指定工作目录，若不存在则会自动创建

ADD：将本地文件添加到容器中，tar类型文件会自动解压(网络压缩资源不会被解压)，可以访问网络资源，类似wget

RUN：构建镜像时执行的命令

CMD：构建容器后调用，也就是在容器启动时才进行调用。

ENTRYPOINT：配置容器，使其可执行化。配合CMD可省去"application"，只使用参数。

ENV：设置环境变量

EXPOSE：声明运行时容器提供服务端口，运行时不会主动开启这个端口的服务

VOLUME：用于指定持久化目录

ARG：用于指定传递给构建运行时的变量

USER:指定运行容器时的用户名或 UID，后续的 RUN 也会使用指定用户。使用USER指定用户时，可以使用用户名、UID或GID，或是两者的组合。当服务不需要管理员权限时，可以通过该命令指定运行用户。并且可以在之前创建所需要的用户
```

### docker-compose

> 文件格式可以指定yml或yaml

docker-compose文件样例：  
```yaml
version: '3'

services:
  service_name:
    image: image_name
    volumes:
    - /etc/localtime:/etc/localtime:ro
    - /etc/timezone:/etc/timezone:ro
    - /local_path/:/remote_server_path/
    ports:
    - "port:port"
    environment:
    - server.port=port
    restart: always
    container_name: container_name
    networks:
    - custom_network

networks:
  custom_network:
    external:
      name: local_network
```

用docker-compose.yml文件创建镜像和容器  
```
docker-compose up -d
```

docker-compose带来的是单机部署上的便利，然而对于跨宿主机的容器的部署和管理能力依然较弱。

#### 问题列表

（1）无法运行`docker-compose`，出现以下信息

```shell
[2053] Cannot open self /usr/local/bin/docker-compose or archive /usr/local/bin/docker-compose.pkg
```

首先确保用户有足够的权限

```
chmod +x /usr/local/bin/docker-compose
```

若上诉命令不起作用的话，可能是版本原因，考虑重装docker-compose

```shell
curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

可以换镜像地址加速下载

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

然后再给docker-compose添加权限即可使用。

### 配置network

docker之所以这么强大是因为它不仅支持容器间相互通信，还支持非docker的工作空间的连接（详情见docker-network[文档](https://docs.docker.com/network/)）。
故配置networks通常用于集群服务，以解决网络隔离问题。

可以在docker种自定义桥接网络  
```
docker network create network_name
```
在docker-compose.yml文件中使用
```
services:
    networks:
      - custom_network
          
networks:
  custom_network:
    external:
      name: network_name
```

## Docker + Jenkins 自动化部署

#### 部署流程结构

1.  开发人员在git仓库上有一个push事件
2.  git仓库把push事件推送到Jenkins
3. Jenkins 获取push源码，编译，打包，构建镜像
4. Jenkins push 镜像到云服务器仓库
5. Jenkins执行远程脚本  
    &nbsp; 5.1 远程服务器pull镜像
    &nbsp; 5.2 停止旧容器，启动新版本容器
6.  项目部署测试

#### Jenkins项目配置文件

核心步骤

1. **源码管理（Source Code Management）**

   添加所要抓取的代码库。代码仓库若是私有的，需要在以前的访问URL上增加用户名和密码的认证信息

2. **构建触发器（Build Triggers）**

   提供身份验证令牌（Authentication Token）激活钩子Webhook Trigger，需要开启git仓库的`webhooks`功能；

3. **构建环境（Build Environment）**

   生成版本信息，类似`${BUILD_DATE_FORMATTED, "yyyyMMdd"}.${BUILDS_TODAY}`的版本号等

4. **构建项目**

   提供执行shell或打包命令，如`clean package -U -DskipTests=true`等

5. **Docker Build and Publish**

   指定构建的镜像仓库名和版本版本标签，提供**Docker registry URL**及凭证。

   某些项目需要指定Dockerfile路径（如未指定则可能报错`unable to prepare context: unable to evaluate symlinks in Dockerfile path`）

6. **构建后操作（Post-build Actions）**

   这里需要通过ssh连接到发布服务所在服务器，指定docker-compose.yml文件，替换版本信息（`sed`指令），指定docker用户登录认证信息，完成构建发布

#### 设置webhook自动抓取代码

设置git的指定操作后推送更新到Jenkins服务来构建项目，需要设置git仓库的Web钩子功能

推送地址格式（git版本为`gogs`）
```text
http(s)://user:API token@Jenkins_URL/generic-webhook-trigger/invoke?token=token_name
或
http(s)://<jenkins-server>/gogs-webhook/?job=<jobname>
```

数据格式  
一般有`application/x-www-form-urlencoded`和`application/json`两种格式，常用json格式  

设置指定**触发Web钩子**的事件（Push、 发布版本等）

#### 登录到私有镜像仓库

使用`docker login`登录
```
docker login -u username -p password registry_hostname
```
可能会出现
```text
Error loading config file: /root/.docker/config.json
```
解决办法：
+ 服务器连接仓库需要配置一个config文件，新建一个config.json文件。

也可能是权限问题，解决办法：
```
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "/home/$USER/.docker" -R
```
登录不上，连接被拒绝的问题
```text
Error response from daemon: Get https://hub.yunzhidata.com/v2/: dial tcp x.x.x.x:443: connect: connection refused
```
解决办法：增加/etc/docker/daemon.json文件
```json
{
    "registry-mirrors": [
        "https://registry.docker-cn.com",
        "http://registry_hostname",
	"http://registry_ip:5000"
    ],
    "insecure-registries": ["registry_hostname","x.x.x.x:5000"]
}
```
然后重启Docker服务
```
systemctl daemon-reload
systemctl restart docker
```
此后就可以通过用户名和密码登录私有镜像仓库了。

### Jenkins访问Docker Daemon服务

#### 问题描述

在Docker安装的Jenkins容器内使用docker命令无效，无法访问到宿主机的Docker Daemon服务

#### 问题背景

Docker分为服务端和客户端，Docker Daemon是Docker的守护进程，Docker客户端通过命令行与Docker Daemon通信

Docker Client与Docker服务端的三种连接方式：
1. UNIX套接字（默认）  
在启动docker服务后，会生成/var/run/docker.sock文件，UNIX套接字用于本地进程之间的通信，所以这种连接方式仅限于本机Docker的客户端访问
2. TCP端口监听  
服务端开启端口监听`dockerd -H IP:PORT`  
客户端需要通过`-H`参数来访问`docker -H IP:PORT`  
这种方式是不安全的，因为暴露Docker服务的端口可能会导致黑客获取宿主机最高权限，造成损失（可以通过启用TLS安全连接进行连接认证）
3. 监听多个Socket

#### 解决办法

通过`docker.sock`采用socket进行连接的方式，需要在创建Jenkins容器的时候挂载宿主机的sock文件

```text
...
-v /home/jenkins/:/var/jenkins_home
-v /var/run/docker.sock:/var/run/docker.sock
-v /usr/bin/docker:/usr/bin/docker
...
```
如果宿主机缺少`docker.sock`文件，需要安装或重启daemon服务


通过TCP端口访问的方式，可以通过dockerd命令指定，也可以编辑宿主机docker.service文件
```text
vim /usr/lib/systemd/system/docker.service
```
修改ExecStart内容
```text
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```
然后重启docker服务
```text
systemctl daemon-reload
systemctl restart docker.service
```
然后在Jenkins容器内就可以使用docker命令，或者是在Jenkins管理端通过**Docker-Build-Step**插件指定**Docker Host URL**中的`tcp://ip:port`方式，就不会出现`Is the docker daemon running?`问题了。

注 : 为防止端口被占用的问题，在开启端口监听前可以通过`ps aux|grep dockerd`或`netstat -lnp|grep port`先来确认端口是否被监听。

### 相关问题列表

1. 错误信息
    ```text
    unknown: Are you trying to mount a directory onto a file (or vice-versa)? Check if the specified host path exists and is the expected type
    ```
    问题原因：volume mount 失败，挂载文件出错，这里是宿主机没有相关文件的错误。只能挂载到目录或已存在的数据容器卷，不能挂载到文件。

2. 错误信息
    ```text
    Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
    ```
    问题原因：不能通过默认的UNIX socket方式连接Docker Daemon服务，需要确认是否有/var/run/docker.sock文件或通过指定tcp监听端口的方式连接Docker Daemon服务。