一个完整的Docker有以下几个部分组成：

- Docker Client客户端
- Docker Daemon守护进程 (docker 服务)
- Docker Image镜像
- Docker Container容器

![Docker 引擎](.\image\Docker-engine.gif)

```bash
# centos7 安装docker
$ sudo yum install docker -y
#卸载 docker
$ yum remove docker-ce
#删除镜像、容器、配置文件等内容：
$ rm -rf /var/lib/docker
```

# Docker 服务

```bash
#启动 docker 服务
$ systemctl start\stop\restart docker
```

# Docker容器（container）

```bash
$ docker start\stop\pause\rm <docker_id/docker_name>
$ docker container start\stop\pause\rm <docker_id/docker_name>

#如果要导出本地某个容器，可以使用 docker export 命令
$ docker export <docker id> > ubuntu.tar
#导入容器快照,可以使用 docker import 从容器快照文件中再导入为镜像，以下实例将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1:
$ cat docker/ubuntu.tar | docker import - test/ubuntu:v1
此外，也可以通过指定 URL 或者某个目录来导入，例如：
$ docker import http://example.com/exampleimage.tgz example/imagerepo

#删除容器使用 docker rm 命令
$ docker rm -f <docker id>

#查看正在运行的docker 容器
$ docker ps
$ docker ps -a

#启动和关闭指定docker 容器
$ docker start/stop/restart <docker id>

#docker容器重命名
$ docker rename <docker id> <name>
```



```bash
#启动容器,
#参数说明：-i: 交互式操作。-t: 终端。ubuntu: ubuntu 镜像。/bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash
$ docker run -it ubuntu /bin/bash
#在大部分的场景下，我们希望 docker 的服务是在后台运行的，可以使用-d 指定容器的运行模式
$ docker run -itd -p 127.0.0.1:5000:5000 --name ubuntu-test ubuntu /bin/bash

#容器启动后，进入容器内部
#docker attach 退出终端，会导致容器的停止。
#docker exec 推荐大家使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。
$ docker attach <docker id>
$ docker exec -it <docker id> /bin/bash

#要退出终端，直接输入 exit:
[root@0602ff96565f core]# exit
exit
```



# Docker镜像（image）

```bash
#下载镜像 
$ docker pull ubuntu
#上传镜像
$ docker push ubuntu

#查看本地镜像
$ docker images
$ docker image ls

#查看镜像的细节，包括镜像层数据和元数据
$ docker image inspect <docker_id/docker_name>

#
$ docker image pull
$ docker image push

#docker 删除不用的镜像,删除悬空的镜像的两种方式
#清理掉所有处于终止状态的容器
$ docker image prune -a -f
$ docker container prune -f

$ docker image rm <docker_id/docker_name>

```

创建和更新镜像

```bash
#创建镜像的2种方式
#1、从已经创建的容器中更新镜像，并且提交这个镜像
#2、使用 Dockerfile 指令来创建一个新的镜像

#更新镜像
#更新镜像之前，我们需要使用镜像来创建一个容器。
$ docker run -t -i ubuntu:15.10 /bin/bash
# ID 为 e218edb10161 的容器是按我们的需求更改的容器。我们可以通过命令 docker commit 来提交容器副本。

$ docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2
sha256:70bf1840fd7c0d2d8ef0a42a817eb29f854c1af8f7c59fc03ac7bdee9545aff8
#各个参数说明：
#-m: 提交的描述信息
#-a: 指定镜像作者
#e218edb10161：容器 ID
#runoob/ubuntu:v2: 指定要创建的目标镜像名
```

## 网络端口映射

```
-P是容器内部端口随机映射到主机的端口
-p是容器内部端口绑定到指定的主机端口
```

## Docker 容器互联

```bash
# 新建网络
$ docker network create -d bridge test-net

#连接容器
#运行一个容器并连接到新建的 test-net 网络:
$ docker run -itd --name test1 --network test-net ubuntu /bin/bash

#打开新的终端，再运行一个容器并加入到 test-net 网络
$ docker run -itd --name test2 --network test-net ubuntu /bin/bash
```

