
<p align="center">
<img width="130" align="center" src="img/logo.svg"/>
</p>
<h1 align="center">Docker入门教程</h1>
Docker 是一个开源的应用容器引擎，而一个<ruby>容器<rt>containers</rt></ruby>其实是一个虚拟化的独立的环境，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

- Docker 的局限性之一，它只能用在 64 位的操作系统上。

# 目录

<!-- TOC -->

- [新版本安装](#新版本安装)
- [旧版本安装](#旧版本安装)
- [命令介绍](#命令介绍)
- [服务管理](#服务管理)
- [镜像管理](#镜像管理)
  - [通过容器创建镜像](#通过容器创建镜像)
  - [通过Dockerfile创建镜像](#通过dockerfile创建镜像)
  - [部署CentOS7容器应用](#部署CentOS7容器应用)
    - [构建包含httpd应用的容器](#构建包含httpd应用的容器)
    - [构建包含openssh-server应用的容器](#构建包含openssh-server应用的容器)
    - [CentOS7的docker容器的一个bug](#CentOS7的docker容器的一个bug)
  - [发布自己的镜像](#发布自己的镜像)
  - [镜像打包](#镜像打包)
  - [镜像中安装软件](#镜像中安装软件)
- [容器管理](#容器管理)
  - [容器命令参数](#容器命令参数)
  - [容器服务管理](#容器服务管理)
  - [Docker容器开机启动设置](#Docker容器开机启动设置)
  - [通过容器创建镜像](#通过容器创建镜像)
  - [进入容器](#进入容器)
  - [容器固定IP](#容器固定IP)
- [文件拷贝](#文件拷贝)
- [Docker私有仓库搭建](#docker私有仓库搭建)
  - [`registry`](#registry)
  - [`Harbor`](#harbor)
- [使用Docker实战](#使用docker实战)
  - [`Nginx`](#nginx)
  - [`MySQL`](#mysql)
  - [`Redis`](#redis)
  - [`Elasticsearch`](#elasticsearch)
  - [`Gitlab`](#gitlab)
  - [`Rocket.Chat`](#rocketchat)
  - [`Humpback`](#humpback)
  - [`Seafile`](#seafile)
  - [更多](#更多)
- [卸载旧的版本](#卸载旧的版本)
- [参考资料](#参考资料)
  - [官方英文资源](#官方英文资源)
  - [中文资源](#中文资源)
  - [其它资源](#其它资源)

<!-- /TOC -->


Docker 从 `1.13` 版本之后采用时间线的方式作为版本号，分为社区版 `CE` 和企业版 `EE`，社区版是免费提供给个人开发者和小型团体使用的，企业版会提供额外的收费服务，比如经过官方测试认证过的基础设施、容器、插件等。

社区版按照 `stable` 和 `edge` 两种方式发布，每个季度更新 `stable` 版本，如 `17.06`，`17.09`；每个月份更新 `edge` 版本，如`17.09`，`17.10`。

下面教程运行在 `Centos` 中

## 新版本安装

Docker 官方的安装教程，[在这里](https://docs.docker.com/install/linux/docker-ce/centos/)。

安装一些必要的系统工具

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加软件源信息

```bash
# docker 官方源
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 阿里云源
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

可选：启用 `edge` 和 `test` 存储库。 这些存储库包含在上面的 `docker.repo` 文件中，但默认情况下处于禁用状态。您可以将它们与稳定存储库一起启用。

```
$ sudo yum-config-manager --enable docker-ce-edge
$ sudo yum-config-manager --enable docker-ce-test
```

您可以通过使用 `--disable` 标志运行 `yum-config-manager` 命令来禁用边缘或测试存储库。 要重新启用它，请使用 `--enable` 标志。 以下命令禁用 `edge` 存储库:

```bash
$ sudo yum-config-manager --disable docker-ce-edge
$ sudo yum-config-manager --disable docker-ce-test
```

安装 Docker-ce

```bash
# 安装前可以先更新 yum 缓存：
sudo yum makecache fast
# 安装 Docker-ce
sudo yum install docker-ce
```

如果你想安装特定 `docker-ce` 版本，先列出 repo 中可用版本，然后选择安装

```bash
$ yum list docker-ce --showduplicates | sort -r
# docker-ce.x86_64            18.06.1.ce-3.el7                   docker-ce-stable
# docker-ce.x86_64            18.06.1.ce-3.el7                   @docker-ce-stable
# docker-ce.x86_64            18.06.0.ce-3.el7                   docker-ce-stable
# docker-ce.x86_64            18.03.1.ce-1.el7.centos            docker-ce-stable
# docker-ce.x86_64            18.03.0.ce-1.el7.centos            docker-ce-stable
# docker-ce.x86_64            17.12.1.ce-1.el7.centos            docker-ce-stable
# 选择版本安装
$ sudo yum install docker-ce-<VERSION STRING>

# 选择安装 docker-ce-18.06.1.ce
$ sudo yum install docker-ce-18.06.1.ce
```

启动 Docker 后台服务

```bash
$ sudo systemctl start docker
```

通过运行 `hello-world` 镜像，验证是否正确安装了 `docker`。

```bash
$ docker run hello-world
```

## 旧版本安装

```bash
yum install docker        # CentOS 中安装
apt-get install docker-ce # Ubuntu 中安装
pacman -S docker          # Arch 中安装
emerge --ask docker       # Gentoo 中安装

#=====================

docker version      # 通过查看版本，检查安装是否成功
# Client:
#  Version:         1.12.6
#  API version:     1.24
#  Package version: docker-1.12.6-55.gitc4618fb.el7.centos.x86_64
#  Go version:      go1.8.3
#  Git commit:      c4618fb/1.12.6
#  Built:           Thu Sep 21 22:33:52 2017
#  OS/Arch:         linux/amd64
# 
# Server:
#  Version:         1.12.6
#  API version:     1.24
#  Package version: docker-1.12.6-55.gitc4618fb.el7.centos.x86_64
#  Go version:      go1.8.3
#  Git commit:      c4618fb/1.12.6
#  Built:           Thu Sep 21 22:33:52 2017
#  OS/Arch:         linux/amd64
```

## 命令介绍

```bash
$ docker --help

管理命令:
  container   管理容器
  image       管理镜像
  network     管理网络
命令：
  attach      介入到一个正在运行的容器
  build       根据 Dockerfile 构建一个镜像
  commit      根据容器的更改创建一个新的镜像
  cp          在本地文件系统与容器中复制 文件/文件夹
  create      创建一个新容器
  exec        在容器中执行一条命令
  images      列出镜像
  kill        杀死一个或多个正在运行的容器    
  logs        取得容器的日志
  pause       暂停一个或多个容器的所有进程
  ps          列出所有容器
  pull        拉取一个镜像或仓库到 registry
  push        推送一个镜像或仓库到 registry
  rename      重命名一个容器
  restart     重新启动一个或多个容器
  rm          删除一个或多个容器
  rmi         删除一个或多个镜像
  run         在一个新的容器中执行一条命令
  search      在 Docker Hub 中搜索镜像
  start       启动一个或多个已经停止运行的容器
  stats       显示一个容器的实时资源占用
  stop        停止一个或多个正在运行的容器
  tag         为镜像创建一个新的标签
  top         显示一个容器内的所有进程
  unpause     恢复一个或多个容器内所有被暂停的进程
```

## 服务管理

```bash
service docker start       # 启动 docker 服务，守护进程
service docker stop        # 停止 docker 服务
service docker status      # 查看 docker 服务状态
chkconfig docker on        # 设置为开机启动
```

## 镜像管理

镜像可以看做我们平时装系统的镜像，里面就是一个运行环境。

```bash
docker search centos						# 搜索docker官方提供的centos镜像
docker search centos --filter=stars=100		# 查找stars数至少为100的镜像
docker pull centos							# 默认从官网拉取
docker pull daocloud.io/library/centos		# 从daocloud拉取
docker dao pull centos						# 从daocloud拉取
docker images              					# 查看已下载的镜像
docker rm image_id         					# 删除镜像，指定镜像id
docker images              					# 查看已下载的镜像
docker images -q		   					# 只查看所有镜像的id
docker inspect imageID	   					# 查看镜像详情
docker rm image_id         					# 删除镜像，指定镜像id
docker rmi RepositoryName --force 			# 删除镜像，指定镜像名, --force镜像在使用中强制删除

# 如果镜像正在被未运行的容器使用，则需要强制删除，但是如果正在被运行的容器使用，则强制删除也无法删除

# 删除所有镜像
# none 默认为 docker.io
docker rmi $(docker images | grep none | awk '{print $3}' | sort -r)
docker rmi $(docker images -q)

# 连接进行进入命令行模式，exit命令退出。
docker run -t -i nginx:latest /bin/bash
```

### 通过容器创建镜像

我们可以通过以下两种方式对镜像进行更改。

1. 从已经创建的容器中更新镜像，并且提交这个镜像
2. 使用 Dockerfile 指令来创建一个新的镜像

下面通过已存在的容器创建一个新的镜像。

```bash
docker commit -m="First Docker" -a="wcjiang" a6b0a6cfdacf wcjiang/nginx:v1.2.1
```

上面命令参数说明：

- `-m` 提交的描述信息
- `-a` 指定镜像作者
- `a6b0a6cfdacf` 记住这个是容器id，不是镜像id
- `wcjiang/nginx:v1.2.1` 创建的目标镜像名

### 通过Dockerfile创建镜像

假设创建一个 node.js 镜像，首先在 node.js 项目根目录创建文件。

```bash
touch Dockerfile .dockerignore
```

`.dockerignore` 文件内容，下面代码表示，这三个路径要排除，不要打包进入 image 文件。如果你没有路径要排除，这个文件可以不新建。

```bash
.git
node_modules
npm-debug.log
```

Dockerfile 文件内容

```Dockerfile
FROM node:8.4
COPY . /app
WORKDIR /app
RUN npm install --registry=https://registry.npm.taobao.org
EXPOSE 3000
```

- `FROM node:8.4`：该 `image` 文件继承官方的 `node image`，冒号表示标签，这里标签是`8.4`，即`8.4`版本的 `node`。
- `COPY . /app`：将当前目录下的所有文件（除了 `.dockerignore` 排除的路径），都拷贝进入 `image` 文件的 `/app` 目录。
- `WORKDIR /app`：指定接下来的工作路径为`/app`。
- `RUN npm install`：在/app目录下，运行 `npm install` 命令安装依赖。注意，安装后所有的依赖，都将打包进入 `image` 文件。
- `EXPOSE 3000`：将容器 `3000` 端口暴露出来， 允许外部连接这个端口。

有了 `Dockerfile` 文件以后，就可以使用 `docker image build` 命令创建 `image` 文件了。

```bash
$ docker image build -t koa-demo .
# 或者
$ docker image build -t koa-demo:0.0.1 .
```

上面命令，`-t` 参数用来指定 `image` 文件的名字，后面还可以用冒号指定标签。如果不指定，默认的标签就是 `latest`。注意后面有个 `.`，表示 Dockerfile 文件所在的路径为当前路径

### 部署CentOS7容器应用

```shell
# 生产生活中经常会涉及到使用基础镜像制作自己的镜像，经常使用的就是系统镜像，但是centos7镜像默认使用systemctl 命令是无法启动服务的。

# 镜像中的包管理问题
	# 默认情况下，为了减小镜像的尺寸，在构建CentOS镜像时用了yum的nodocs选项，如果你安装一个包发现文件缺失，请在/etc/yum.conf中注释掉tsflogs=nodocs之后重新安装。

# systemd管理服务问题
	# 在centos7之前使用init管理各种服务，centos7开始使用system管理系统中所有的服务，但因为systemd要求capsysadmin权限才能读取到宿主机cgroup(资源限制的能力)；另CentOS7中用fakesystemd代替了systemd来解决依赖问题，但如果使用fakesystem无法使用应用，故需要修改回systemd，
# 无法启动服务也就无法远程连接
[root@a3c8baf6961e .ssh]# systemctl restart sshd.service
Failed to get D-Bus connection: Operation not permitted
```



#### 构建包含httpd应用的容器

```shell
1、使用dockerfile创建镜像
[root@aliyun ~]# mkdir centos7
[root@aliyun ~]# cd centos7/
[root@aliyun centos7]# vim Dockerfile
FROM daocloud.io/library/centos:latest
MAINTAINER "xiaochen"  xiaochen@qq.com
ENV container docker
RUN yum -y swap -- remove fakesystemd -- install systemd systemd-libs
RUN yum -y update; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;
VOLUME [ "/sys/fs/cgroup" ]
CMD ["/usr/sbin/init"]

# 删除fakesystemd并安装systemd，然后再构建基础镜像
[root@aliyun centos7]# docker build --rm -t local/c7-systemd .

# 构建包含httpd应用的容器
# 1、使用构建之后的基础镜像再去构建包含systemd应用的镜像
[root@aliyun centos7]# mv Dockerfile  Dockerfile.bak
[root@aliyun centos7]# vim Dockerfile
[root@aliyun centos7]# cat Dockerfile
FROM local/c7-systemd
RUN yum -y install httpd; yum clean all; systemctl enable httpd.service
EXPOSE 80
CMD ["/usr/sbin/init"]

[root@aliyun centos7]# docker build --rm -t local/c7-systemd-httpd .

# 2、运行包含systemd的httpd的应用容器
# 需要使用--privileged选项,并且挂载主机的 cgroups 文件夹。
[root@aliyun centos7]# docker run --privileged -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -p 80:80 local/c7-systemd-httpd

# 注意：不能添加/bin/bash，添加了会导致服务不可用，而且有些服务可能会发现之前提到的权限不够的问题，但是如果不加会运行在前台(没有用-d)，可以用ctrl+p+q放到后台去

# 3、访问测试  -p 80:88   IP:88
```

#### 构建包含openssh-server应用的容器

```bash
[root@aliyun centos7]# cat Dockerfile
FROM local/c7-systemd
RUN yum -y install openssh-server; yum clean all; systemctl enable sshd.service
RUN echo 1 | passwd --stdin root
EXPOSE 22
CMD ["/usr/sbin/init"]

[root@aliyun centos7]# docker build --rm -t local/c7-systemd-sshd .

[root@aliyun centos7]# docker run --privileged -ti -v /sys/fs/cgroup:/sys/fs/cgroup:ro -p 2222:22 local/c7-systemd-sshd

[root@localhost ~]# ssh 39.108.140.0 -p 2222
```

#### CentOS7的docker容器的一个bug

```bash
centos7镜像创建的容器里面安装服务后，不能用systemctl/service启动服务，centos6的容器里没有这个坑！
原因是：dbus-daemon没能启动

[root@a3c8baf6961e .ssh]# systemctl restart sshd.service
Failed to get D-Bus connection: Operation not permitted

治标不治本的解决方案如下：
将CMD设置为/usr/sbin/init即可。这样就会自动将dbus等服务启动起来，即采用 /usr/sbin/init自动启动dbus daemon。在启动容器时，加上参数--privileged和/sbin/init即可
 
[root@localhost ~]#  docker run --privileged -i -t library/centos /sbin/init     
上面的容器启动后，会一直在卡着的状态中，先不用管，打开另一个终端窗口，查看容器。这里注意，宿主机可能会出现注销当前登陆账户的情况
 
[root@localhost ~]# docker ps
然后按照容器的ID进去，这个时候再根据/bin/bash进入容器（前面加exec -it参数），接着重启ssh服务就ok了
[root@localhost ~]# docker exec -it af40bd07fa0f /bin/bash
[root@af40bd07fa0f /]# systemctl restart sshd.service

这个解决方案有很大的坑：
1、卡住不动的问题
2、打开图形就会先把图形重启
```







### 镜像打包

方案一:export

​	利用export把正在运行的容器直接导出为tar包的镜像文件，可以用-o或>

```shell
docker run --name my-nginx -d -p 8080:80 some-centent-nginx:1.2
docker export my-nginx > youmen_nginx.tar  &&  docker export -o youmen_nginx.tar my-nginx
scp youmen_nginx.tar 120.77.248.31:
docker import youmen_nginx.tar
docker tag 121d8 mynginx:1							# 设置镜像名字
docker import youmen_nginx.tar mynginx:1.1			# 导入时即设置镜像名字
```

方案二：利用save直接把镜像打包出来

```bash
docker save -o suibian.tar library/centos:latest 
scp suibian.tar 192.168.135.161:
docker load < suibian.tar				# 导入之后使用原名
```



### 发布自己的镜像

1. 在[Docker](https://www.docker.com/) 注册账户，发布的镜像都在[这个页面里](https://cloud.docker.com/repository/list)展示
2. 将上面做的镜像`nginx`，起个新的名字`nginx-test`

```bash
docker tag wcjiang/nginx:v1.2.1 wcjiang/nginx-test:lastest
```

3. 登录docker

```
docker login
```

4. 上传`nginx-test`镜像

```bash
docker push wcjiang/nginx-test:lastest
# The push refers to a repository [docker.io/wcjiang/nginx-test]
# 2f5c6a3c22e3: Mounted from wcjiang/nginx
# cf516324493c: Mounted from wcjiang/nginx
# lastest: digest: sha256:73ae804b2c60327d1269aa387cf782f664bc91da3180d10dbd49027d7adaa789 size: 736
```

### 镜像中安装软件

通常情况下，使用docker官方镜像，如 mysql镜像，默认情况下镜像中啥软件也没有，通过下面命令安装你所需要的软件：

```bash
# 第一次需要运行这个命令，确保源的索引是最新的
# 同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引
apt-get update
# 做过上面更新同步之后，可以运行下面的命令了
apt-get install vim
```

如果你安装了CentOS或者Ubuntu系统可以进入系统安装相关软件

```bash
# 进入到centos7镜像系统
docker run -i -t centos:7 /bin/bash
yum update
yum install vim
```

## 容器管理

容器就像一个类的实例

```bash
# 列出本机正在运行的容器
docker container ls

# 列出本机所有容器，包括终止运行的容器
docker container ls --all
docker start [containerID/Names] # 启动容器
docker stop [containerID/Names]  # 停止容器
docker rm [containerID/Names]    # 删除容器
docker logs [containerID/Names]  # 查看日志
docker exec -it [containerID/Names] /bin/bash  # 进入容器

# 从正在运行的 Docker 容器里面，将文件拷贝到本机，注意后面有个【点】拷贝到当前目录
docker container cp [containID]:[/path/to/file] .
docker run centos echo "hello world"  # 在docker容器中运行hello world!
docker run centos yum install -y wget # 在docker容器中，安装wget软件
docker ps                           # 列出包括未运行的容器
docker ps -a                        # 查看所有容器(包括正在运行和已停止的)
docker ps -a -q						# 查看所有容器的ID
docker ps -qf status=running		# 查看某种状态的容器ID
docker ps -l						# 列出最近一次启动的容器
docker inspect  7657b3785bcf		# 查看容器详细配置信息，包含容器名，环境变量，运行命令，主机配置，网络配置，数据卷配置等，json格式；
docker inspect -f {{.State.Pid}} 44fc0f0582d9 # 获取id为 44fc0f0582d9 的PID进程编号
docker inspect --format'{{.Config.Image}}' 7657b3485
daocloud.io/library/centos
docker inspect --format='{{.NetworkSettings.IPAddress}}' 7657b3485
docker logs my-nginx                # 查看 my-nginx 容器日志
docker run -i -t centos /bin/bash   # 启动一个容器
docker commit 8bd centos  			# 保存对容器的修改
docker commit -m "n changed" my-nginx my-nginx-image # 使用已经存在的容器创建一个镜像
docker pull gitlab/gitlab-ce:11.2.3-ce.0	# 下载指定版本容器镜像

#########################################################################################
# 创建容器运行在后台
# 容器在后台运行时，所有I/O数据只能通过网络资源或者共享卷组来进行交互
docker run -it -d php centos /bin/bash

# 打印该容器输出
docker run -it -d --name test centos /bin/bash -c "while true; do echo hello world;sleep 2;done"
docker logs test
# 监控容器运行
docker logs container_id/container_name
--tail:			# 选项可以指定查看最后几条日志
-t:				# 选项则可以对日志条目附加时间戳
-f:				# 选项可以跟踪日志的输出，直到手动停止

# 运行远程机器上的容器
docker run -it -d -h 39.108.140.0 daocloud.io/centos:7

########################################################################################
# 断开容器
# 断开与容器的连接，并且关闭容器
[root@7968b4436989 /]# exit
[root@7968b4436989 /]# docker stop 7968b443

# 只断开和容器的连接而不关闭容器
	快捷键： ctrl+p+q

# 关闭运行中的容器
# 如果此时有其他终端正在对他进行交互会自动中断
# docker stop contrainer_id/name		//发送SIGTERM信号，可忽略，15信号
# docker kill contrainer_id/name		//发送SIGKILL信号，9信号
```

### 容器命令参数

```shell
# 同一个镜像可以启动多个容器
# 创建运行容器且连接到容器
docker run -it --rm --cidfile="id.txt" centos /bin/bash
-i					# 捕获标准输入输出，保持交互式的意思
-t					# 分配一个终端或控制台，每一个控制台都要伴随一个shell
--rm				# 退出时就删除该容器，默认情况下，每个容器在退出时，他的文件系统会保存下来，这样一方面有利于调试，因为可以通过查看日志等方式来确定最终状态；另一方面，也可以报错容器所产生的数据，如果仅仅需要短暂的运行一个容器，且不需要保存容器中的数据，就可以在exit容器时自动清理掉容器及产生的数据，但此选项不能与-d共用
-d					# 创建容器运行在后台
/bin/bash			# 容器运行起来之后运行的程序，也可以是任何的命令，/bin/echo  hello
--cidfile			# 指定容器运行之后container长id的存放文件位置
```

### 容器服务管理

```bash
docker start my-nginx               		# 【启动】一个已经存在的容器
docker restart my-nginx             		# 【重启】容器
docker stop my-nginx                		# 【停止运行】一个容器
docker kill my-nginx                		# 【杀死】一个运行中的容器
docker rename my-nginx new-nginx    		# 【重命名】容器
docker rm new-nginx                 		# 【删除】容器
```

### Docker容器开机启动设置

```shell
# Docker容器开机启动设置
sudo docker run --restart=always -it centos /bin/bash
--restart=always	# 默认情况下docker重启之后所有容器会被关闭，这个选项的意思是容器随docker engine自启动
# 如果创建时候未指定--restart=always,可通过docker  update命令设置：
docker update --restart=always  7b5f30fe77c0	
# 注意Docker服务开启启动
```

restart参数介绍

- no:容器退出时候，不重启容器

- on-failure: 只有在非0状态退出时才重新启动容器

- always:无论退出状态是如何，都重启容器

- unless-stopped: 在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器

  在使用on-failure策略时，指定Docker将尝试重新启动容器的最大次数；

  默认情况下，Docker将尝试永远重新启动容器

  ```
  sudo docker run --restart=on-failure:5  <image>
  ```

### 通过容器创建镜像

我们可以通过以下两种方式对镜像进行更改。

1. 从已经创建的容器中更新镜像，并且提交这个镜像
2. 使用 Dockerfile 指令来创建一个新的镜像

下面通过已存在的容器创建一个新的镜像。

```bash
docker commit -m="First Docker" -a="wcjiang" a6b0a6cfdacf wcjiang/nginx:v1.2.1
```

上面命令参数说明：

- `-m` 提交的描述信息
- `-a` 指定镜像作者
- `a6b0a6cfdacf` 记住这个是容器id，不是镜像id
- `wcjiang/nginx:v1.2.1` 创建的目标镜像名

### 进入容器

1. 创建一个守护状态的Docker容器

```bash
docker run -itd my-nginx /bin/bash
```

2. 使用`docker ps`查看到该容器信息

```bash
docker ps
# CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
# 6bd0496da64f        nginx               "/bin/bash"         20 seconds ago      Up 18 seconds       80/tcp              high_shirley
```

3. 使用`docker exec`命令进入一个已经在运行的容器

```shell
# 连接关闭的容器
# 连接关闭但运行的容器
[root@docker-39 ~]#  ps -q 
7b5f30fe77c0
[root@docker-39 ~]# docker attach 7b5f
[root@7b5f30fe77c0 /]#

# 连接关闭且未运行的容器
docker ps -a -qf status=exited
7968b44369
[root@docker-39 ~]# docker start 7968b44369
7968b44369
[root@docker-39 ~]# docker attach 7968b44369			# 会附加该容器的标准输出到当前命令行


# 启动状态的容器，执行任务
# 通过exec命令可以创建两种任务：后台型任务和交互型任务
# 后台型任务：docker exec -it test /bin/bash
# 交互型任务：docker attach 7968
```

通常有下面几种方式进入Docker的容器，推荐使用`exec`，使用`attach`一直进入失败。

- 使用`docker attach`
- 使用`SSH` [为什么不需要在 Docker 容器中运行 sshd](http://www.oschina.net/translate/why-you-dont-need-to-run-sshd-in-docker?cmp)
- 使用`nsenter`进入Docker容器，[nsenter官方仓库](https://github.com/jpetazzo/nsenter)
- 使用`docker exec`，在`1.3.*`之后提供了一个新的命令`exec`用于进入容器



### 容器固定IP

```bash
# 容器不需要有固定ip，生产中用k8s，k8s不用容器的ip地址，当然k8s也不会直接用容器，用的是pod，pod也需要用ip地址，而是用标签的。这个只为了面试的时候应付面试的，实际应用没有太大作用。
docker的网络类型
1、查看docker默认的三种网络类型
[root@aliyun ~]# docker network list
NETWORK ID            NAME                DRIVER              SCOPE
90b22f633d2f          bridge              bridge              local
e0b365da7fd2          host                host                local
da7b7a090837         none                null                local

bridge:网络桥接
        默认情况下启动、创建容器都是用该模式，所以每次docker容器重启时会按照顺序获取对应ip地址，这就导致容器每次重启，ip都发生变化

none：无指定网络
    启动容器时，可以通过–network=none,docker容器不会分配局域网ip

host：主机网络
        docker容器的网络会附属在主机上，两者是互通的。

2、创建固定ip容器
1）、创建自定义网络类型，并且指定网段
[root@aliyun ~]# docker network create --subnet=192.168.0.0/16 staticnet
d0806acb2cce0f6c53089c7bd4218d4590a5eb3b014b9ca9a5e0eaa113e8ecb7
[root@aliyun ~]# docker network  list     
NETWORK ID          NAME         DRIVER             SCOPE
93db3aba13ab        bridge              bridge              local
29315fe6b3d5        host                host                local
0a44ab133923        none                null                local
d0806acb2cce        staticnet           bridge              local

2）、使用新的网络类型创建并启动容器
[root@aliyun ~]# docker run -it --name userserver --net staticnet --ip 192.168.0.2  daocloud.io/library/centos:7
[root@203a8a8ab5da /]# 
[root@aliyun ~]# docker inspect 203a8a |grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "192.168.0.2",
[root@aliyun ~]# docker stop 203a8a     //重启容器，观察地址是否发生变化
203a8a
[root@aliyun ~]# docker start 203a8a
203a8a
[root@aliyun ~]# docker inspect 203a8a |grep -i ipaddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "192.168.0.2",
```



## 文件拷贝

从主机复制到容器 `sudo docker cp host_path containerID:container_path`  
从容器复制到主机 `sudo docker cp containerID:container_path host_path`


## Docker私有仓库搭建

通过官方提供的私有仓库镜像`registry`来搭建私有仓库。通过 [humpback](https://humpback.github.io) 快速搭建轻量级的Docker容器云管理平台。关于仓库配置说明请参见[configuration.md](https://github.com/docker/distribution/blob/master/docs/configuration.md)

> ⚠️ 注意：也可以通过部署管理工具 `Harbor` 来部署 `registry`

除了 [Harbor](https://github.com/goharbor/harbor) 还有 [humpback](https://github.com/humpback/humpback) 和 [rancher](https://github.com/rancher/rancher)

### `registry`

```bash
docker pull registry:2.6.2
```

创建容器并运行，创建成功之后，可访问 `http://192.168.99.100:7000/v2/`，来检查仓库是否正常运行，当返回 `{}` 时，表示部署成功。

```bash
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  registry:2

# 自定义存储位置
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  -v $HOME/_docker/registry:/var/lib/registry \
  registry:2

docker run -d -p 5000:5000 --restart=always --name registry \
    -v `pwd`/config.yml:/etc/docker/registry/config.yml \
    registry:2
```

推送镜像到私有仓库

```bash
# 从官方仓库拉取一个镜像
docker pull nginx:1.13
# 为镜像 `nginx:1.13` 创建一个新标签 `192.168.31.69:7000/test-nginx:1.13`
docker tag nginx:latest 192.168.31.69:5000/test-nginx:1.13
# 推送到私有仓库中
docker push 192.168.31.69:5000/test-nginx:1.13
# The push refers to a repository [192.168.99.100:7000/test-nginx]
# Get https://192.168.99.100:7000/v1/_ping: http: server gave HTTP response to HTTPS client
```

在推送到的时候报错误，默认是使用`https`提交，这个搭建的默认使用的是 `http`，解决方法两个：

1. 创建一个https映射
2. 将仓库地址加入到不安全的仓库列表中

我们使用第二种方法，加入到不安全的仓库列表中，修改docker配置文件`vi /etc/docker/daemon.json` 添加 `insecure-registries`配置信息，如果 [daemon.json](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file) 文件不存在可以创建，关键配置项，将仓库将入到不安全的仓库列表中。

```js
{
  "insecure-registries":[ 
    "192.168.31.69:5000"
  ]
}
```

>  如果是 macOS 可以通过 docker 客户端，`Preferences` => `Advanced` => `添加配置` => `Apply & Restart`，重启docker就可以了。  

重启服务 `service docker restart`，默认情况下 push 是会报如下错误的：

```bash
docker push 192.168.99.100:7000/test-nginx:1.13
# The push refers to a repository [192.168.99.100:7000/test-nginx]
# a1a53f8d99b5: Retrying in 1 second
# ...
# received unexpected HTTP status: 500 Internal Server Error
```

上面错误是 `SELinux` 强制访问控制安全系统，阻止导致的错误，通过下面方法禁用 SELinux 之后就可以 push 了。

```bash
setenforce 0  
getenforce   
# Permissive  
```

```bash
# 停止本地 registry
docker container stop registry
# 要删除容器，请使用 docker container rm
docker container stop registry && docker container rm -v registry
# 自定义存储位置
```

### `Harbor`

[部署 registry 管理工具 Harbor](docker/harbor.md)

## 使用Docker实战

> ⚠文件挂载注意：docker 禁止用主机上不存在的文件挂载到 container 中已经存在的文件

```bash
-d, --detach=false      # 指定容器运行于前台还是后台，默认为false   
-i, --interactive=false # 打开STDIN，用于控制台交互  
-t, --tty=false         # 分配tty设备，该可以支持终端登录，默认为false  
-u, --user=""           # 指定容器的用户  
-a, --attach=[]         # 登录容器（必须是以docker run -d启动的容器）
-w, --workdir=""        # 指定容器的工作目录 
-c, --cpu-shares=0      # 设置容器CPU权重，在CPU共享场景使用  
-e, --env=[]            # 指定环境变量，容器中可以使用该环境变量  
-m, --memory=""         # 指定容器的内存上限  
-P, --publish-all=false # 指定容器暴露的端口  
-p, --publish=[]        # 指定容器暴露的端口 
-h, --hostname=""       # 指定容器的主机名  
-v, --volume=[]         # 给容器挂载存储卷，挂载到容器的某个目录  
--volumes-from=[]       # 给容器挂载其他容器上的卷，挂载到容器的某个目录
--cap-add=[]            # 添加权限，权限清单详见：http://linux.die.net/man/7/capabilities  
--cap-drop=[]           # 删除权限，权限清单详见：http://linux.die.net/man/7/capabilities  
--cidfile=""            # 运行容器后，在指定文件中写入容器PID值，一种典型的监控系统用法  
--cpuset=""             # 设置容器可以使用哪些CPU，此参数可以用来容器独占CPU  
--device=[]             # 添加主机设备给容器，相当于设备直通  
--dns=[]                # 指定容器的dns服务器  
--dns-search=[]         # 指定容器的dns搜索域名，写入到容器的/etc/resolv.conf文件  
--entrypoint=""         # 覆盖image的入口点  
--env-file=[]           # 指定环境变量文件，文件格式为每行一个环境变量  
--expose=[]             # 指定容器暴露的端口，即修改镜像的暴露端口  
--link=[]               # 指定容器间的关联，使用其他容器的IP、env等信息  
--lxc-conf=[]           # 指定容器的配置文件，只有在指定--exec-driver=lxc时使用  
--name=""               # 指定容器名字，后续可以通过名字进行容器管理，links特性需要使用名字  
--net="bridge"          # 容器网络设置:
                            # bridge 使用docker daemon指定的网桥     
                            # host 	//容器使用主机的网络  
                            # container:NAME_or_ID  >//使用其他容器的网路，共享IP和PORT等网络资源  
                            # none 容器使用自己的网络（类似--net=bridge），但是不进行配置 
--privileged=false      # 指定容器是否为特权容器，特权容器拥有所有的capabilities  
--restart="no"          # 指定容器停止后的重启策略:
                            # no：容器退出时不重启  
                            # on-failure：容器故障退出（返回值非零）时重启 
                            # always：容器退出时总是重启  
--rm=false              # 指定容器停止后自动删除容器(不支持以docker run -d启动的容器)  
--sig-proxy=true        # 设置由代理接受并处理信号，但是SIGCHLD、SIGSTOP和SIGKILL不能被代理
```

### `Nginx`

[在 docker 中部署 Nginx](docker/nginx.md)

### `MySQL`

[在 docker 中部署 MySQL](docker/mysql.md)

### `Redis`

[在 docker 中部署 Redis](docker/redis.md)

### `Elasticsearch`

[在 docker 中部署 Elasticsearch](docker/elasticsearch.md)

### `Gitlab`

[在 docker 中部署 Gitlab](docker/gitlab.md)

### `Rocket.Chat`

[在 docker 中部署 Gitlab](docker/rocket.chat)

### `Humpback`

首先创建放持久化数据文件夹，`mkdir -p /opt/app/humpback-web`，里面存放持久化数据文件，会存储站点管理和分组信息，启动后请妥善保存。



```bash
# 创建放持久化数据文件夹
mkdir -p /opt/app/humpback-web
# 下载humpback-web镜像到本地
docker pull humpbacks/humpback-web:1.0.0
# 启动 humpback-web 容器，将容器命名为 humpback-web
docker run -d --net=host --restart=always \
 -e HUMPBACK_LISTEN_PORT=7001 \
 -v /opt/app/humpback-web/dbFiles:/humpback-web/dbFiles \
 --name humpback-web \
 humpbacks/humpback-web:1.0.0
 
 # 客户端上传镜像
vim /etc/docker/daemon.json
{
  "insecure-registries":[
    "39.108.140.0:7001"
  ]
}
docker tag 98ebf73 39.108.140.0:7001/nginx
docker push 39.108.140.0:7001/nginx
```

访问站点，打开浏览器输入：http://192.168.99.100:7001 ，默认账户：`admin` 密码：`123456`

### `Seafile`

```
docker run -d --name seafile \
  -e SEAFILE_SERVER_HOSTNAME=seafile.example.com \
  -v /opt/seafile-data:/shared \
  -p 80:80 \
  seafileltd/seafile:latest
```

```
docker run -d --name seafile \
  -e SEAFILE_SERVER_HOSTNAME=pan.showgold.com \
  -e SEAFILE_ADMIN_EMAIL=wcj@nihaosi.com \
  -e SEAFILE_ADMIN_PASSWORD=wcj@nihaosi.com \
  -v $HOME/_docker/seafile-data:/shared \
  -p 80:80 \
  seafileltd/seafile:latest
```

### 更多

[更多 docker 工具部署，在 docker 目录中](docker/)

## 卸载旧的版本

移除旧的版本

```bash
$ sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-selinux \
    docker-engine-selinux \
    docker-engine
```

## 参考资料

### 官方英文资源

- Docker官网：http://www.docker.com
- Docker windows入门：https://docs.docker.com/windows/
- Docker Linux 入门：https://docs.docker.com/linux/
- Docker mac 入门：https://docs.docker.com/mac/
- Docker 用户指引：https://docs.docker.com/engine/userguide/
- Docker 官方博客：http://blog.docker.com/
- Docker Hub: https://hub.docker.com/
- Docker开源： https://www.docker.com/open-source

### 中文资源

- Docker中文网站：http://www.docker.org.cn
- Docker中文文档：http://www.dockerinfo.net/document
- Docker安装手册：http://www.docker.org.cn/book/install.html
- 一小时Docker教程 ：https://blog.csphere.cn/archives/22
- Docker中文指南：http://www.widuu.com/chinese_docker/index.html

### 其它资源

- [Docker 快速手册！](https://github.com/eon01/DockerCheatSheet)
- [Docker 教程](http://www.runoob.com/docker/docker-tutorial.html)
- [Docker 从入门到实践](https://www.gitbook.com/book/yeasy/docker_practice)
- [MySQL Docker 单一机器上如何配置自动备份](http://blog.csdn.net/zhangchao19890805/article/details/52756865)
- [使用Docker Compose管理多个容器](http://dockone.io/article/834)
- https://segmentfault.com/t/docker
- https://github.com/docker/docker
- https://wiki.openstack.org/wiki/Docker
- https://wiki.archlinux.org/index.php/Docker
