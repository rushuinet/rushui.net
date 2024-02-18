---
title: "Dockerfile文件详解"
date: 2024-02-17T22:12:46+08:00
keywords: ["docker","Dockerfile"]
description: "Docker 可以通过读取 Dockerfile 中的指令自动构建镜像。 Dockerfile 是一个文本文档，其中包含了用户创建镜像的所有命令和说明。"
categories: ["技术文档"]
tags: ["docker","Dockerfile"]
thumbnail: ""
banner: ""
---
Docker 可以通过读取 Dockerfile 中的指令自动构建镜像。 Dockerfile 是一个文本文档，其中包含了用户创建镜像的所有命令和说明。
## 指令详解
|Dockerfile| 指令说明 |
|--|--|
|FROM|	指定基础镜像，用于后续的指令构建。|
|MAINTAINER|	指定Dockerfile的作者|/维护者。（已弃用，推荐使用LABEL指令）
|LABEL|	添加镜像的元数据，使用键值对的形式。|
|RUN|	在构建过程中在镜像中执行命令。|
|CMD|	指定容器创建时的默认命令。（可以被覆盖）|
|ENTRYPOINT|	设置容器创建时的主要命令。（不可被覆盖）|
|EXPOSE|	声明容器运行时监听的特定网络端口。|
|ENV|	在容器内部设置环境变量。|
|ADD|	将文件、目录或远程URL复制到镜像中。|
|COPY|	将文件或目录复制到镜像中。|
|VOLUME|	为容器创建挂载点或声明卷。|
|WORKDIR|	设置后续指令的工作目录。|
|USER|	指定后续指令的用户上下文。|
|ARG|	定义在构建过程中传递给构建器的变量，可使用 "docker build" 命令设置。|
|ONBUILD|	当该镜像被用作另一个构建过程的基础时，添加触发器。|
|STOPSIGNAL|	设置发送给容器以退出的系统调用信号。|
|HEALTHCHECK|	定义周期性检查容器健康状态的命令。|
|SHELL|	覆盖Docker中默认的shell，用于RUN、CMD和ENTRYPOINT指令。|

## 最佳实践
### nginx
```dockerfile
FROM ubuntu:latest
MAINTAINER rushui "rushui@qq.com"
# RUN apt-get update
# RUN apt-get install -y vim 

# RUN apt-get install -y nginx
# 以上执行会创建 3 层镜像。可简化为以下格式：
RUN apt-get update && apt-get install -y vim &&  apt-get install -y nginx
# 如上，以 && 符号连接命令，这样执行后，只会创建 1 层镜像。
#指定运行该镜像的容器使用的端口为 80
# docker run的时候 一定要加上 -P
EXPOSE 80
CMD ["nginx","-g","daemon off;"]
```
### 多阶段构建golang
https://github.com/go-zeus/demo/blob/main/Dockerfile
```dockerfile
FROM golang:1.16-alpine AS build
WORKDIR /go/src/project/
COPY . /go/src/project/
RUN go build -ldflags "-s -w" -o /bin/project
FROM alpine:3.5
RUN apk add --update curl && rm -rf /var/cache/apk/*
COPY --from=build /bin/project /bin/project
EXPOSE 8080
CMD ["/bin/project"]
```
### 多阶段构建golang后端与vue前端
- .dockerignore文件
```
/ui/dist
/ui/node_modules
```
- Dockerfile
```dockerfile
FROM golang:1.21-alpine AS admin
WORKDIR /project
COPY . /project/
RUN go env -w GOPROXY=https://goproxy.cn,direct && go build -ldflags "-s -w" -o /project/kb
FROM node:21.6-alpine AS ui
WORKDIR /project
COPY ./ui /project/
RUN npm install -g pnpm@8.3.1 && pnpm i && pnpm build-only
FROM alpine:3.18
MAINTAINER rushui
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk add --update curl && rm -rf /var/cache/apk/*
WORKDIR /app
COPY --from=admin /project/kb /app/kb
COPY --from=ui /project/dist /app/ui/dist
EXPOSE 8080
CMD ["./kb"]
```

## 一、 变量
变量用 $variable_name 或者 ${variable_name} 表示

- ${variable:-word}表示如果variable设置，则结果将是该值。如果variable未设置，word则将是结果。
- ${variable:+word}表示如果variable设置则为word结果，否则为空字符串。
变量前加 \ 可以转义成普通字符串：\$fooor\${foo}，表示转换为$foo和${foo}文字。

## 二、FROM
初始化一个新的构建阶段，并设置基础映像：
```dockerfile
FROM [--platform=<platform>] <image> [AS <name>]
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```
- 单个dockfile可以多次出现 FROM ，以使用之前的构建阶段作为另一个构建阶段的依赖项
- AS name表示为构建阶段命名，在后续FROM和COPY --from=<name>说明中可以使用这个名词，引用此阶段构建的映像
- digest其实就是就是根据镜像内容产生的一个ID，只要镜像的内容不变digest也不会变
- tag或digest值是可选的。如果您省略其中任何一个，构建器默认使用一个latest标签。如果找不到该tag值，构建器将返回错误。
- --platform标志可用于在FROM引用多平台镜像的情况下指定平台。例如，linux/amd64、linux/arm64、 或windows/amd64


## 三、RUN
将在当前镜像之上的新层中执行命令，在 docker build时运行
```dockerfile
RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'
```
RUN有两种形式：
```dockerfile
RUN <command>（shell 形式，命令在 shell 中运行，默认/bin/sh -c在 Linux 或cmd /S /CWindows 上）
RUN ["executable", "param1", "param2"]（执行形式）
```
说明：

- 可以使用\（反斜杠）将单个 RUN 指令延续到下一行
- RUN在下一次构建期间，指令缓存不会自动失效。可以使用--no-cache标志使指令缓存无效
- Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大，可以使用&& 符号连接命令，这样执行后，只会创建 1 层镜像
## 四、CMD
运行程序，在docker run 时运行，但是和run命令不同，RUN 是在 docker build时运行
```dockerfile
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```
支持三种格式
```dockerfile
CMD ["executable","param1","param2"] 使用 exec 执行，推荐方式；
CMD command param1 param2 在 /bin/sh 中执行，提供给需要交互的应用；
CMD ["param1","param2"] 提供给 ENTRYPOINT 的默认参数；
```
指定启动容器时执行的命令，每个 Dockerfile 只能有一条 CMD 命令。如果指定了多条命令，只有最后一条会被执行。

如果用户启动容器时候指定了运行的命令，则会覆盖掉 CMD 指定的命令。



## 五、LABEL
添加元数据
```dockerfile
LABEL multi.label1="value1" \
      multi.label2="value2" \
      other="value3"
```

## 六、EXPOSE
```dockerfile
EXPOSE <port> [<port>/<protocol>...]
```
Docker 容器在运行时侦听指定的网络端口。可以指定端口是监听TCP还是UDP，如果不指定协议，默认为TCP。

该EXPOSE指令实际上并未发布端口。要在运行容器时实际发布端口，docker run -P 来发布和映射一个或多个端口。

默认情况下，EXPOSE假定 TCP。您还可以指定 UDP：
```dockerfile
EXPOSE 80/udp
```

## 七、ENV
设置环境变量
```dockerfile
ENV <key>=<value> ...
```
设置的环境变量将持续存在，您可以使用docker inspect来查看。使用docker run --env <key>=<value>来更改环境变量的值。

如果环境变量只在构建期间需要，请考虑为单个命令设置一个值：
```dockerfile
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y ...
```
或者使用ARG，它不会保留在最终镜像中：
```dockerfile
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y ...
```

## 八、ADD
复制新文件、目录或远程文件 URL <src> ，并将它们添加到<dest>中。

<src>可以指定多个资源，但如果它们是文件或目录，则它们的路径被解释为相对于构建上下文的源，也就是WORKDIR。

每个都<src>可能包含通配符，匹配将使用 Go 的 filepath.Match规则。例如：

添加所有以“hom”开头的文件：
```dockerfile
ADD hom* /mydir/
```
在下面的示例中，?被替换为任何单个字符，例如“home.txt”。
```dockerfile
ADD hom?.txt /mydir/
```
<dest>是一个绝对路径，或相对WORKDIR的相对路径。

## 九、COPY
语法同ADD一致，复制拷贝文件。

COPY指令和ADD指令的唯一区别在于：是否支持从远程URL获取资源。COPY指令只能从执行docker build所在的主机上读取资源并复制到镜像中。而ADD指令还支持通过URL从远程服务器读取资源并复制到镜像中。

相同需求时，推荐使用COPY指令。ADD指令更擅长读取本地tar文件并解压缩。

## 十、ENTRYPOINT
ENTRYPOINT 和 CMD 一样，都是在指定容器启动程序及参数，不过它不会被 docker run 的命令行参数指定的指令所覆盖。如果要覆盖的话，需要通过docker run --entrypoint 来指定。

它有2种格式：
```dockerfile
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```
当指定了 ENTRYPOINT 后， CMD 的内容作为参数传给 ENTRYPOINT 指令，实际执行时，将变为：
```dockerfile
<ENTRYPOINT> <CMD>
```

## 十一、VOLUME
创建一个具有指定名称的挂载数据卷
```dockerfile
VOLUME ["/var/log/"]
VOLUME /var/log
```
它的主要作用是：

- 避免重要的数据，因容器重启而丢失
- 避免容器不断变大
## 十二、ARG
定义变量，与ENV 作用相同，不过ARG变量不会像ENV变量那样持久化到构建好的镜像中。
```dockerfile
ARG <name>[=<default value>]
```
Docker 有一组预定义的ARG变量，您可以在 Dockerfile 中没有相应指令的情况下使用这些变量。

- HTTP_PROXY
- http_proxy
- HTTPS_PROXY
- https_proxy
- FTP_PROXY
- ftp_proxy
- NO_PROXY
- no_proxy
要使用这些，请使用--build-arg标志在命令行上传递它们，例如：
```dockerfile
 docker build --build-arg HTTPS_PROXY=https://my-proxy.example.com .
```

## 十三、ONBUILD
将一个触发指令添加到镜像中，以便稍后在该镜像用作另一个构建的基础时执行。也就是另外一个dockerfile FROM了这个镜像的时候执行。
```dockerfile
ONBUILD ADD . /app/src
ONBUILD RUN /usr/local/bin/python-build --dir /app/src
```

## 十四、STOPSIGNAL
设置将发送到容器退出的系统调用信号。该信号可以是与内核系统调用表中的位置匹配的有效无符号数，例如 9，或格式为 SIGNAME 的信号名称，例如 SIGKILL。
```dockerfile
STOPSIGNAL signal
```
默认的 stop-signal 是 SIGTERM，在docker stop的时候会给容器内PID为1的进程发送这个signal，通过--stop-signal 可以设置自己需要的signal，主要目的是为了让容器内的应用程序在接收到signal之后可以先处理一些事物，实现容器的平滑退出，如果不做任何处理，容器将在一段时间之后强制退出，会造成业务的强制中断，默认时间是10s。

## 十五、HEALTHCHECK
用于指定某个程序或者指令来监控 docker 容器服务的运行状态。该HEALTHCHECK指令有两种形式：

- HEALTHCHECK [OPTIONS] CMD command （通过在容器内运行命令来检查容器健康状况）
- HEALTHCHECK NONE （禁用从基础镜像继承的任何健康检查）


## 十六、SHELL
覆盖用于命令的shell 形式的默认 shell。Linux 上的默认 shell 是["/bin/sh", "-c"]，Windows 上是["cmd", "/S", "/C"]
```dockerfile
SHELL ["executable", "parameters"] 
```
该SHELL指令在 Windows 上特别有用，因为 Windows 有两种常用且截然不同的本机 shell：cmd和powershell，以及可用的备用 shell，包括sh。该SHELL指令可以出现多次。每条SHELL指令都会覆盖所有先前的SHELL指令，并影响所有后续指令。

## 十七、WORKDIR
工作目录，如果WORKDIR不存在，即使它没有在后续Dockerfile指令中使用，它也会被创建。

docker build 构建镜像过程中，每一个 RUN 命令都会新建一层。只有通过 WORKDIR 创建的目录才会一直存在。

可以设置多个WORKDIR，如果提供了相对路径，它将相对于前一条WORKDIR指令的路径。例如：
```dockerfile
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd 
```
最终pwd命令的输出是/a/b/c.

该WORKDIR指令可以解析先前使用 ENV，例如：
```dockerfile
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```
最终pwd命令的输出是/path/$DIRNAME


## 十八、USER
设置用户名（或 UID）和可选的用户组（或 GID）
```dockerfile
USER <user>[:<group>]
USER <UID>[:<GID>]
```
