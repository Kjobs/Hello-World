## Docker容器

### 单独的jar包在Docker种的部署方式

1. 构建好的jar包、Dockerfile文件以及resources、lib各种依赖文件上传至服务器某目录（例：`/data/docker/demo`）下

2. 编译Dockerfile文件`docker build -t image_name .`

3. 为镜像创建容器，并指定端口映射`docker run -d --name container_name -p 8080:8080 image_name`

4. 查看容器是否创建并运行成功`docker ps`

5. 查看容器日志`docker logs -f -tail 100 container_id`

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

### docker-compose文件

> 文件格式可以指定yml或yaml



### Docker + Jenkins 自动化部署

需要注意几点：
1. 代码仓库若是私有的，需要在以前的访问URL上增加用户名和密码的认证信息
2. 构建触发方式，需要使用git仓库的webhooks功能
3. 指定构建完成后Dockerfile路径（如未指定则可能报错`unable to prepare context: unable to evaluate symlinks in Dockerfile path`）
4. 构建项目，指定docker-compose.yml文件，指定docker用户登录认证信息，完成构建发布