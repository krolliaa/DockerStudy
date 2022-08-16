# `Docker`

## 1.初识`Docker`

### 1. 1 `Docker`可以解决的问题

项目部署的问题：大型项目组件较多，运行环境也较为服务咋，部署时会碰到一些问题

- 依赖关系复杂，容易出现兼容性问题
- 解决了安装时的依赖关系配置但是不同的环境如开发、测试、生产存在差异

![img](https://img-blog.csdnimg.cn/d66962f3487140fc9f34b9ba10ebd5bb.png)

`Docker`一来可以解决混乱依赖的问题：

- `Docker`会将应用中的`Libs`（函数库）、`Deps`（依赖）、配置与应用一起打包，形成可移植镜像
- 将每个应用都放到一个隔离容器中去运行，使用沙箱机制，相互隔离

当你需要安装的时候将整个函数库、依赖以及原本的应用当作一个应用全部打包，因为所处位置在不同的容器中，所以各种依赖和函数库是互不干扰的。

![img](https://img-blog.csdnimg.cn/a049d0cdf0ae4b9d92f7bb72de40bad4.png)

除此之外，`Docker`还可以解决跨系统运行：

- `Docker`将用户程序与所需要调用的系统（比如`Ubuntu`）函数库一起打包【太牛了...】
- `Docker`运行到不同操作系统时，直接基于打包的库函数，借助于操作系统的`Linux`内核来运行即仅依赖系统的`Linux`内核即可运行

![img](https://img-blog.csdnimg.cn/8eb71b12255a40cba4933a64636f93ef.png)

### 1.2 `Docker`与虚拟机

`Docker`与虚拟机：【一般都使用的是`Docker`】

- `Docker`是一个系统进程，虚拟机则是`OS`中的`OS`。虚拟机需要通过`Hypervisor`才能完成工作。
- `Docker`的性能接近原生，体积小速度快，虚拟机的性能较差，体积大速度慢。

![img](https://img-blog.csdnimg.cn/6b1bccef34d1444c8ccbfec3075e8353.png)

### 1.3 `Docker`架构

**镜像和容器**

- 镜像`Image`：`Docker`将应用程序及其所需要的依赖、函数库、环境、配置文件等等都打包到一起称为镜像。
- 容器`Container`：镜像中的应用程序运行后形成的进程就是容器，只是`Docker`会给容器做隔离，对外不可见。

![img](https://img-blog.csdnimg.cn/23b7ff1fe19846e9ad18bcf4261bf82e.png)

如何将镜像共享给他人使用呢？你可以自己搭建私有云，或者使用公有云等等，这些镜像托管平台类似于`Github`，我们称之为：`DockerHub`。【`Docker Registry`】

![img](https://img-blog.csdnimg.cn/0a743bbaa11c4282a19ad177fdadfc03.png)

`Docker`是一个`CS`架构：

- 服务端：`Docker`守护进程，负责处理`Docker`指令，管理镜像、容器等【**接收命令**】
- 客户端：通过命令或者`RestAPI`向`Docker`服务端发送命令，可以在本地或者远程向服务端发送指令【**发送命令**】

![img](https://img-blog.csdnimg.cn/33ce003bbd464873ae048c29e5e5df25.png)

## 2. `CentOS`安装`Docker`

`Docker` 分为`CE`和`EE`两大版本。`CE`即社区版（免费，支持周期`7`个月），`EE`即企业版，强调安全，付费使用，支持周期`24`个月。

`Docker CE`分为`stable` `test` 和 `nightly` 三个更新频道。

官方网站上有各种环境下的[安装指南](https://docs.docker.com/install/)，这里主要介绍`Docker CE`在`CentOS`上的安装。`Docker CE`支持`64`位版本`CentOS 7`，并且要求内核版本不低于`3.10`。

### 2.1 卸载（可选）

如果之前安装过旧版本的`Docker`，可以使用下面命令卸载：

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```

### 2.2 安装docker

首先需要大家虚拟机联网，安装`yum`工具

```shell
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```

然后更新本地镜像源：

```shell
# 设置docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```

然后输入命令：

```shell
yum install -y docker-ce
```

`docker-ce`为社区免费版本。稍等片刻，docker即可安装成功。

### 2.3 启动docker

`Docker`应用需要用到各种端口，逐一去修改防火墙设置。非常麻烦，因此建议大家直接关闭防火墙！

启动docker前，一定要关闭防火墙后！！

启动docker前，一定要关闭防火墙后！！

启动docker前，一定要关闭防火墙后！！

```sh
# 关闭
systemctl stop firewalld
# 禁止开机启动防火墙
systemctl disable firewalld
```

通过命令启动`docker`：

```sh
systemctl start docker  # 启动docker服务

systemctl stop docker  # 停止docker服务

systemctl restart docker  # 重启docker服务
```

然后输入命令，可以查看`docker`版本，查看得到说明`docker`启动成功：

```
docker -v
```

如图：![img](https://img-blog.csdnimg.cn/8e1c61c24497427d8de2015bb9046b69.png)

### 2.4 配置镜像加速

docker官方镜像仓库网速较差，我们需要设置国内镜像服务：

参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

## 3. `Docker`的基本操作

### 3.1 镜像名称

首先来看下镜像的名称组成：

- 镜名称一般分两部分组成：`[repository]:[tag]`。
- 在没有指定`tag`时，默认是`latest`，代表最新版本的镜像

![img](https://img-blog.csdnimg.cn/fad02b44cf244b4295df9d82bb6d1fc6.png)

这里的`mysql`就是`repository`，`5.7`就是`tag`，合一起就是镜像名称，代表`5.7`版本的`MySQL`镜像。

### 3.2 镜像命令

常见的镜像操作命令如图：`docker --help / docker images --help`

![img](https://img-blog.csdnimg.cn/7753a3599c3f4988ab73ed525b2f4858.png)

#### 3.2.1 案例1-拉取、查看镜像

需求：`从DockerHub`中拉取一个`nginx`镜像并查看

1）首先去镜像仓库搜索`nginx`镜像，比如[`DockerHub`](https://hub.docker.com/):

![img](https://img-blog.csdnimg.cn/d7a9d3372b314f35bc649749ec0bb954.png)

2）根据查看到的镜像名称，拉取自己需要的镜像，通过命令：`docker pull nginx`

![img](https://img-blog.csdnimg.cn/a3de7472aa794578a0f2d82667ead43d.png)

3）通过命令：`docker images`查看拉取到的镜像

![img](https://img-blog.csdnimg.cn/6159e75656ee4b39a8af7755a0189a4f.png)

#### 3.2.2 案例2-保存、导入镜像

需求：利用`docker save`将`nginx`镜像导出磁盘，然后再通过`load`加载回来

1）利用`docker xx --help`命令查看`docker save`和`docker load`的语法

例如，查看`save`命令用法，可以输入命令：

```sh
docker save --help
```

![img](https://img-blog.csdnimg.cn/8c753358c17b4569be4f12e6cc6fd2b8.png)

命令格式：

```shell
docker save -o [保存的目标文件名称] [镜像名称]
```

2）使用`docker save`导出镜像到磁盘 

```sh
docker save -o nginx.tar nginx:latest
```

![img](https://img-blog.csdnimg.cn/6438e1d61e954c2e873dcf08266b5baa.png)

3）使用`docker load`加载镜像

先删除本地的`nginx`镜像：

```sh
docker rmi nginx:latest
```

然后运行命令，加载本地文件：

```sh
docker load -i nginx.tar
```

![img](https://img-blog.csdnimg.cn/490bee0dd9fc4b858a523016803350eb.png)

#### 3.2.3 镜像练习

需求：去`DockerHub`搜索并拉取一个`Redis`镜像

目标：

1. 去`DockerHub`搜索`Redis`镜像

2. 查看`Redis`镜像的名称和版本

3. 利用`docker pull`命令拉取镜像

   ```shell
   docker pull redis:latest
   docker images redis
   ```

4. 利用`docker save`命令将`redis:latest`打包为一个`redis.tar.gz`包

   ```shell
   docker save -o redis.tar.gz redis:latest
   ```

5. 利用`docker rmi`删除本地的`redis:latest`

   ```shell
   docker rmi redis:latest
   ```

6. 利用`docker load`重新加载`redis.tar.gz`文件

   ```shell
   docker load -i redist.tar.gz
   ```

### 3.3 容器操作

#### 3.3.1.容器相关命令

容器操作的命令如图：

![img](https://img-blog.csdnimg.cn/8c2e78f02d7f4644aa616442ab85918e.png)

- `docker run`：创建并运行一个容器，处于运行状态
- `docker pause`：让一个运行的容器暂停
- `docker unpause`：让一个容器从暂停状态恢复运行
- `docker stop`：停止一个运行的容器
- `docker start`：让一个停止的容器再次运行
- `docker rm`：删除一个容器
- `docker ps`：查看一个容器运行状态
- `docker logs [containerName]`：查看容器运行日志

容器保护三个状态：

- 运行：进程正常运行
- 暂停：进程暂停，`CPU`不再运行，并不释放内存
- 停止：进程终止，回收进程占用的内存、`CPU`等资源

#### 3.3.2 案例-创建并运行一个容器

创建并运行`nginx`容器的命令：

```sh
docker run --name [containerName容器名称] -p 80:80 -d [imageName镜像名称]
```

命令解读：

- `docker run`：创建并运行一个容器
- `--name`: 给容器起一个名字，比如叫做`mn`
- `-p`：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口，默认情况下，容器是隔离环境，我们直接访问宿主机的`80`端口，肯定访问不到容器中的`nginx`。现在，将容器的`80`与宿主机的`80`关联起来，当我们访问宿主机的`80`端口时，就会被映射到容器的`80`，这样就能访问到`nginx`了。
- `-d`：后台运行容器
- `imageName`：镜像名称，例如`nginx`

![img](https://img-blog.csdnimg.cn/a970a4496f5a4f4999b34553aff1cae6.png)

```shell
docker ps 查看容器详细信息
```

这里的虚拟机网络是`NAT`模式，需要在`Virtual Box`做一些设置，然后访问：`http://192.168.56.1:80`成功访问：

![img](https://img-blog.csdnimg.cn/e7d9ede123c64069b6c65187653f8093.png)

查看日志：

```shell
docker logs NginxContainer
```

每次都要自己手动输入很麻烦，可否持续查看日志？

```shell
docker logs --help
docker logs NginxContainer -f
```

![img](https://img-blog.csdnimg.cn/b11280b8db6f4b3abf67fea47ef5b21c.png)

#### 3.3.3 案例-进入容器，修改文件

**需求**：进入`Nginx`容器，修改`HTML`文件内容，添加`“Hello World”`

**提示**：进入容器要用到`docker exec`命令。

**步骤**：

1. 进入容器。进入我们刚刚创建的`nginx`容器的命令为：

   ```shell
   docker exec -it NginxContainer bash
   ```

   命令解读：

   - `docker exec`：进入容器内部，执行一个命令

   - `-it`: 给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互

   - `mn`：要进入的容器的名称

   - `bash`：进入容器后执行的命令，`bash`是一个`linux`终端交互命令

2. 进入`nginx`的HTML所在目录`/usr/share/nginx/html`

   容器内部会模拟一个独立的`Linux`文件系统，看起来如同一个`linux`服务器一样：

   ![img](https://img-blog.csdnimg.cn/638583bb8598423caf4405d7633ccbaa.png)`nginx`的环境、配置、运行文件全部都在这个文件系统中，包括我们要修改的`html`文件。查看`DockerHub`网站中的`nginx`页面，可以知道`nginx`的`html`目录位置在`/usr/share/nginx/html`

   我们执行命令，进入该目录：

   ```shell
   cd /usr/share/nginx/html
   ```

    查看目录下文件：![img](https://img-blog.csdnimg.cn/00605131ab87402faf1eaedf882b9b99.png)

3. 修改`index.html`的内容

   容器内没有vi命令，无法直接修改，我们用下面的命令来修改：

   ```shell
   sed -i -e 's#Welcome to nginx#Hello World#g' -e 's#<head>#<head><meta charset="utf-8">#g' index.html
   ```

4. 在浏览器访问`Nginx:80`地址

   ![img](https://img-blog.csdnimg.cn/82dd6e085dbd413e92ab08e225d5768b.png)

#### 3.3.4.小结

- `docker run`命令的常见参数有哪些？

  - `--name`：指定容器名称

  - `-p`：指定端口映射

  - `-d`：让容器后台运行


- 查看容器日志的命令：

  - `docker logs [containerName]`

  - 添加`-f`参数可以持续查看日志 ---> `docker logs --help`


- 查看容器状态：

  - `docker ps`

  - `docker ps -a`查看所有容器，包括已经停止的


#### 3.3.5 容器练习

需求：创建并运行一个`Redis`容器，并且支持数据持久化

```shell
docker pull redis:latest
docker images redis:latest
docker save -o redis.tar.gz redis:latest
docker rmi redis.latest
docker load -i redis.tar.gz

docker run --name RedisContainer -p 6379:6379 -d redis:latest
docker exec -it RedisContainer bash
docker exec -it RedisContainer redis-cli
```

### 3.4 数据卷（容器数据管理）

在之前的`nginx`案例中，修改`nginx`的`html`页面时，需要进入`nginx`内部。并且因为没有编辑器，修改文件也很麻烦。这就是因为容器与数据（容器内文件）耦合带来的后果。要解决这个问题，必须将数据与容器解耦，这就要用到**数据卷**了。

![img](https://img-blog.csdnimg.cn/495209b746694845af1c7b9e33e9ca98.png)

#### 3.4.1.什么是数据卷

**数据卷`（volume）`**是一个虚拟目录，指向宿主机文件系统中的某个目录。一旦完成数据卷挂载，对容器的一切操作都会作用在数据卷对应的宿主机目录了。这样，我们操作宿主机的`/var/lib/docker/volumes/html`目录，就等于操作容器内的`/usr/share/nginx/html`目录了。【**这不就跟`windows`的快捷方式一样的嘛？**】

**可以直接修改、更新，而且删除掉容器但是没删数据卷，数据就还在，可以重新挂载。**

![img](https://img-blog.csdnimg.cn/cb9194c92e2c483bbc4aadb50a89f05b.png)

#### 3.4.2.数据集操作命令

数据卷操作的基本语法如下：

```sh
docker volume [COMMAND] create/ls/inspect/prune/rm
```

`docker volume`命令是数据卷操作，根据命令后跟随的command来确定下一步的操作：

- `create`创建一个`volume`
- `inspect`显示一个或多个`volume`+的信息
- `ls`列出所有的`volume`
- `prune`删除未使用的`volume`
- `rm`删除一个或多个指定的`volume`

#### 3.4.3.创建和查看数据卷

**需求**：创建一个数据卷，并查看数据卷在宿主机的目录位置

① 创建数据卷

```sh
docker volume create html
```

② 查看所有数据

```sh
docker volume ls
```

![img](https://img-blog.csdnimg.cn/71344ef7409f46aab25cb40245d66897.png)

③ 查看数据卷详细信息卷

```sh
docker volume inspect html
```

![img](https://img-blog.csdnimg.cn/0e12c7d612c645bb9115b7bad6e7356f.png)

可以看到，我们创建的`html`这个数据卷关联的宿主机目录为`/var/lib/docker/volumes/html/_data`目录。

**小结**：

数据卷的作用：

- 将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全

数据卷操作：

- `docker volume create`：创建数据卷
- `docker volume ls`：查看所有数据卷
- `docker volume inspect [VolumeName]`：查看数据卷详细信息，包括关联的宿主机目录位置
- `docker volume rm`：删除指定数据卷
- `docker volume prune`：删除所有未使用的数据卷

#### 3.4.4 挂载数据卷

我们在创建容器时，可以通过`-v`参数来挂载一个数据卷到某个容器内目录，命令格式如下：

```sh
docker run --name mn -v html:/root/html -p 8080:80 -d nginx
```

这里的`-v`就是挂载数据卷的命令：

- `-v html:/root/htm` ：把`html`数据卷挂载到容器内的`/root/html`这个目录中

#### 3.4.5.案例-给`nginx`挂载数据卷

**需求**：创建一个`nginx`容器，修改容器内的`html`目录内的`index.html`内容

**分析**：上个案例中，我们进入`nginx`容器内部，已经知道`nginx`的`html`目录所在位置`/usr/share/nginx/html`，我们需要把这个目录挂载到`html`这个数据卷上，方便操作其中的内容。

**提示**：运行容器时使用`-v`参数挂载数据卷

步骤：

① 创建容器并挂载数据卷到容器内的`HTML`目录

```sh
docker run --name mn -v html:/usr/share/nginx/html -p 80:80 -d nginx
```

② 进入`html`数据卷所在位置，并修改`HTML`内容

```sh
# 查看html数据卷的位置
docker volume inspect html
# 进入该目录
cd /var/lib/docker/volumes/html/_data
# 修改文件
vim index.html
```

**<font color="red">注：如果该数据卷不存在，使用`docker run`创建容器时会自动创建数据卷。非常智能。</font>**

![img](https://img-blog.csdnimg.cn/6caa3bb77d654c69a2ac1e5a2930686e.png)

#### 3.4.6 案例-给MySQL挂载本地目录

容器不仅仅可以挂载数据卷，也可以直接挂载到宿主机目录上。关联关系如下：

- 带数据卷模式：宿主机目录 --> 数据卷 ---> 容器内目录
- 直接挂载模式：宿主机目录 ---> 容器内目录

**语法**：

目录挂载与数据卷挂载的语法是类似的：

- -v [宿主机目录]:[容器内目录]
- -v [宿主机文件]:[容器内文件]

**需求**：创建并运行一个`MySQL`容器，将宿主机目录直接挂载到容器

实现思路如下：

1）将资料中的`mysql.tar`文件上传到虚拟机，通过`load`命令加载为镜像

2）创建目录`/tmp/mysql/data`

3）创建目录`/tmp/mysql/conf`，将资料提供的`hmy.cnf`文件上传到`/tmp/mysql/conf`

4）去`DockerHub`查阅资料，创建并运行`MySQL`容器，要求：

① 挂载`/tmp/mysql/data`到`mysql`容器内数据存储目录

② 挂载`/tmp/mysql/conf/hmy.cnf`到`mysql`容器的配置文件

③ 设置`MySQL`密码

```shell
docker run --name MySQLContainer -e MYSQL_ROOT_PASSWORD=123 -p 3306:3306 -v /tmp/mysql/conf/hmy.cnf:/etc/mysql/cnf.d/hmt.conf -v /tmp/mysql/data:/var/lib/mysql -d mysql:5.7.25
```

如果搞错了需要删除容器跟数据卷：

```shell
docker rm -f MySQLContainer
docker volume prune
```

#### 3.4.7.小结

`docker run`的命令中通过`-v`参数挂载文件或目录到容器中：

- `-v volume`名称:容器内目录
- `-v`宿主机文件:容器内文
- `-v`宿主机目录:容器内目录

数据卷挂载与目录直接挂载的

- 数据卷挂载耦合度低，由`docker`来管理目录，但是目录较深不好找
- 目录挂载耦合度高，需要我们自己管理目录，不过目录容易寻找查看

## 4. `Dockerfile`自定义镜像

**常见的镜像在`DockerHub`就能找到，但是我们自己写的项目，比如微服务那些就必须自己构建镜像了。而要自定义镜像，就必须先了解镜像的结构才行。**

### 4.1 镜像结构

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成。

我们以`MySQL`为例，来看看镜像的组成结构：在基础镜像的基础上每做一些操作就形成了一层。

![img](https://img-blog.csdnimg.cn/6f4b9f12d8bd44edb61914df95ec1c14.png)

简单来说，镜像就是在系统函数库、运行环境基础上，添加应用程序文件、配置文件、依赖文件等组合，然后编写好启动脚本打包在一起形成的文件。我们要构建镜像，其实就是实现上述打包的过程。

### 4.2 `Dockerfile`语法

**构建自定义的镜像时，并不需要一个个文件去拷贝，打包。我们只需要告诉`Docker`，我们的镜像的组成，需要哪些`BaseImage`、需要拷贝什么文件、需要安装什么依赖、启动脚本是什么，将来`Docker`会帮助我们构建镜像。而描述上述信息的文件就是`Dockerfile`文件。**

**`Dockerfile`**就是一个文本文件，其中包含一个个的**指令(`Instruction`)**，用指令来说明要执行什么操作来构建镜像。每一个指令都会形成一层`Layer`。

![img](https://img-blog.csdnimg.cn/4a92b13fc05e4a31af01e8801ca865ad.png)

更新详细语法说明，请参考官网文档： https://docs.docker.com/engine/reference/builder

### 4.3.构建`Java`项目

#### 4.3.1.基于`Ubuntu`构建`Java`项目

需求：基于`Ubuntu`镜像构建一个新镜像，运行一个`java`项目

- 步骤1：新建一个空文件夹`docker-demo`

- 步骤2：拷贝课前资料中的`docker-demo.jar`文件到`docker-demo`这个目录

- 步骤3：拷贝课前资料中的`jdk8.tar.gz`文件到`docker-demo`这个目录

- 步骤4：拷贝课前资料提供的`Dockerfile`到`docker-demo`这个目录

  其中的内容如下：

  ```dockerfile
  # 指定基础镜像
  FROM ubuntu:16.04
  # 配置环境变量，JDK的安装目录
  ENV JAVA_DIR=/usr/local
  
  # 拷贝jdk和java项目的包
  COPY ./jdk8.tar.gz $JAVA_DIR/
  COPY ./docker-demo.jar /tmp/app.jar
  
  # 安装JDK
  RUN cd $JAVA_DIR \
   && tar -xf ./jdk8.tar.gz \
   && mv ./jdk1.8.0_144 ./java8
  
  # 配置环境变量
  ENV JAVA_HOME=$JAVA_DIR/java8
  ENV PATH=$PATH:$JAVA_HOME/bin
  
  # 暴露端口
  EXPOSE 8090
  # 入口，java项目的启动命令
  ENTRYPOINT java -jar /tmp/app.jar
  ```
  
- 步骤5：进入`docker-demo`

  将准备好的`docker-demo`上传到虚拟机任意目录，然后进入docker-demo目录下

- 步骤6：运行命令：

  ```sh
  docker build -t javaweb:1.0 .
  ```


最后访问 http://192.168.150.101:8090/hello/count，其中的ip改成你的虚拟机ip 

#### 8.3.2 基于`java8`构建`Java`项目【重点，4.3.1 升级版】

虽然我们可以基于`Ubuntu`基础镜像，添加任意自己需要的安装包，构建镜像，但是却比较麻烦。所以大多数情况下，我们都可以在一些安装了部分软件的基础镜像上做改造。

例如，构建`java`项目的镜像，可以在已经准备了`JDK`的基础镜像基础上构建。

```dockerfile
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录
ENV JAVA_DIR=/usr/local

# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar

# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8

# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
```

需求：基于`java:8-alpine`镜像，将一个Java项目构建为镜像

实现思路如下：

- ① 新建一个空的目录，然后在目录中新建一个文件，命名为`Dockerfile`

- ② 拷贝资料提供的`docker-demo.jar`到这个目录中

- ③ 编写`Dockerfile`文件：

  - a ）基于`java:8-alpine`作为基础镜像

  - b ）将`app.jar`拷贝到镜像中

  - c ）暴露端口

  - d ）编写入口`ENTRYPOINT`

    内容如下：

    ```dockerfile
    FROM java:8-alpine
    COPY ./app.jar /tmp/app.jar
    EXPOSE 8090
    ENTRYPOINT java -jar /tmp/app.jar
    ```

- ④ 使用`docker build`命令构建镜像，比如：`docker build javaweb:1.0 .`

- ⑤ 使用`docker run`创建容器并运行，比如：`docker run --name web -p 8090:8090 - d javaweb:1.0`

### 4.4 小结

1. `Dockerfile`的本质是一个文件，通过指令描述镜像的构建过程
2. `Dockerfile`的第一行必须是`FROM`，从一个基础镜像来构建
3. 基础镜像可以是基本操作系统，如`Ubuntu`。也可以是其他人制作好的镜像，例如：`java:8-alpine`

![img](https://img-blog.csdnimg.cn/b36050f092954b7398ec10ad6496a25e.png)

## 5. `Docker-Compose`

一直手动一个个创建和运行容器实在是太繁琐太厌恶了！！！

`Docker Compose`可以基于`Compose`文件帮我们快速的部署分布式应用，而无需手动一个个创建和运行容器！【章鱼手上拿的就是容器 —— 好帮手】

![img](https://img-blog.csdnimg.cn/27dbcbf3c3d44a348358e219afd2a73e.png)

### 5.1 初识`DockerCompose`

`Compose`文件是一个文本文件，通过指令定义集群中的每个容器如何运行。格式如下：

```json
version: "3.8"
 services:
  mysql:
    image: mysql:5.7.25
    environment:
     MYSQL_ROOT_PASSWORD: 123456
    volumes:
     - "/tmp/mysql/data:/var/lib/mysql"
     - "/tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/hmy.cnf"
  web:
    build: .
    ports:
     - "8090:8090"

```

上面的`Compose`文件就描述一个项目，其中包含两个容器：

- `mysql`：一个基于`mysql:5.7.25`镜像构建的容器，并且挂载了两个目录
- `web`：一个基于`docker build`临时构建的镜像容器，映射端口时8090

**注：版本有`1.x 2.x 3.x`。**

`DockerCompose`的详细语法参考官网：https://docs.docker.com/compose/compose-file/

其实`DockerCompose`文件可以看做是将多个`docker run`命令写到一个文件，只是语法稍有差异。

### 5.2 安装`DockerCompose`

#### 5.2.1 下载

`Linux`下需要通过命令下载：

```sh
# 安装
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

如果下载速度较慢，或者下载失败，可以使用资料提供的`docker-compose`文件，上传到`/usr/local/bin/`目录也可以。

#### 5.2.3 修改文件权限

修改文件权限：

```sh
# 修改权限
chmod +x /usr/local/bin/docker-compose
```

#### 5.2.4 `Base`自动补全命令

```sh
# 补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

如果这里出现错误，需要修改自己的hosts文件：

```sh
echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
```

### 5.3.部署微服务集群

**需求**：将之前学习的`cloud-demo`微服务集群利用`DockerCompose`部署

**实现思路**：

① 查看资料提供的`cloud-demo`文件夹，里面已经编写好了`docker-compose`文件

② 修改自己的`cloud-demo`项目，将数据库、`nacos`地址都命名为`docker-compose`中的服务名

③ 使用`maven`打包工具，将项目中的每个微服务都打包为`app.jar`

④ 将打包好的`app.jar`拷贝到`cloud-demo`中的每一个对应的子目录中

⑤ 将`cloud-demo`上传至虚拟机，利用`docker-compose up -d`来部署

#### 5.3.1.`compose`文件

查看课前资料提供的`cloud-demo`文件夹，里面已经编写好了`docker-compose`文件，而且每个微服务都准备了一个独立的目录：

![img](https://img-blog.csdnimg.cn/3791a22b815f4ad3beeef430f42b4eed.png)

`docker-compose.yml`内容如下：

```yaml
version: "3.2"

services:
  nacos:
    image: nacos/nacos-server
    environment:
      MODE: standalone
    ports:
      - "8848:8848"
  mysql:
    image: mysql:5.7.25
    environment:
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "$PWD/mysql/data:/var/lib/mysql"
      - "$PWD/mysql/conf:/etc/mysql/conf.d/"
  userservice:
    build: ./user-service
  orderservice:
    build: ./order-service
  gateway:
    build: ./gateway
    ports:
      - "10010:10010"
```

可以看到，其中包含`5`个`service`服务：

- `nacos`：作为注册中心和配置中心
  - `image: nacos/nacos-server`： 基于`nacos/nacos-server`镜像构建
  - `environment`：环境变量
    - `MODE: standalone`：单点模式启动
  - `ports`：端口映射，这里暴露了`8848`端口
- `mysql`：数据库
  - `image: mysql:5.7.25`：镜像版本是`mysql:5.7.25`
  - `environment`：环境变量
    - `MYSQL_ROOT_PASSWORD: 123`：设置数据库`root`账户的密码为`123`
  - `volumes`：数据卷挂载，这里挂载了`mysql`的`data、conf`目录，其中有我提前准备好的数据
- `userservice`、`orderservice`、`gateway`：都是基于`Dockerfile`临时构建的

查看`mysql`目录，可以看到其中已经准备好了`cloud_order、cloud_user`表：

![img](https://img-blog.csdnimg.cn/c3c9a40b362f48768f276b36fe96da7b.png)

查看微服务目录，可以看到都包含`Dockerfile`文件：

![img](https://img-blog.csdnimg.cn/752660cd948046f9a6c8cd9e60f68d67.png)

内容如下：

```dockerfile
FROM java:8-alpine
COPY ./app.jar /tmp/app.jar
ENTRYPOINT java -jar /tmp/app.jar
```

#### 5.3.2 修改微服务配置

因为微服务将来要部署为`docker`容器，而容器之间互联不是通过`IP`地址，而是通过容器名。这里我们将`order-service`、`user-service`、`gateway`服务的`mysql`、`nacos`地址都修改为基于容器名的访问。

如下所示：

```yaml
spring:
  datasource:
    url: jdbc:mysql://mysql:3306/cloud_order?useSSL=false
    username: root
    password: 123
    driver-class-name: com.mysql.jdbc.Driver
  application:
    name: orderservice
  cloud:
    nacos:
      server-addr: nacos:8848 # nacos服务地址
```

#### 5.3.3 打包

接下来需要将我们的每个微服务都打包。因为之前查看到`Dockerfile`中的`jar`包名称都是`app.jar`，因此我们的每个微服务都需要用这个名称。

可以通过修改`pom.xml`中的打包名称来实现，每个微服务都需要修改：

```xml
<build>
  <!-- 服务打包的最终名称 -->
  <finalName>app</finalName>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

打包后：

![img](https://img-blog.csdnimg.cn/82f09b7fa8004ceb9aaab2d8ae06dafa.png)

#### 5.3.4 拷贝jar包到部署目录

编译打包好的`app.jar`文件，需要放到`Dockerfile`的同级目录中。注意：每个微服务的`app.jar`放到与服务名称对应的目录，别搞错了。

`user-service`：

![img](https://img-blog.csdnimg.cn/9bd5cb3327544d58a320a0a9d041f392.png)

`order-service`：

![img](https://img-blog.csdnimg.cn/19a3318047a74eda84e261ff55fd3031.png)

`gateway`：

![img](https://img-blog.csdnimg.cn/752660cd948046f9a6c8cd9e60f68d67.png)

#### 5.3.5 部署

最后，我们需要将文件整个`cloud-demo`文件夹上传到虚拟机中`/tmp/cloud-demo`，使用`DockerCompose`部署。可以上传到任意目录：

![img](https://img-blog.csdnimg.cn/a2e82ca1812a4c1b9215507321494920.png)

部署：进入`cloud-demo`目录，然后运行下面的命令：

```sh
docker-compose up -d
```

浏览器访问地址：

- `http://192.168.56.1:10010/user/1?authorization=admin`
- `http://192.168.56.1:10010/order/101?authorization=admin`

## 6. Docker镜像仓库 

### 6.1 搭建私有镜像仓库

搭建镜像仓库可以基于`Docker`官方提供的`DockerRegistry`来实现。官网地址：https://hub.docker.com/_/registry

#### 6.1.1 简化版像仓库

`Docker`官方的`Docker Registry`是一个基础版本的`Docker`镜像仓库，具备仓库管理的完整功能，但是没有图形化界面。搭建方式比较简单，命令如下：

```sh
docker run -d \
    --restart=always \
    --name registry	\
    -p 5000:5000 \
    -v registry-data:/var/lib/registry \
    registry
```

命令中挂载了一个数据卷`registry-data`到容器内的`/var/lib/registry`目录，这是私有镜像库存放数据的目录。访问http://YourIp:5000/v2/_catalog可以查看当前私有镜像服务中包含的镜像

#### 6.1.2 带有图形化界面版本

使用`DockerCompose`部署带有图象界面的`DockerRegistry`，命令如下：将其放到某个文件夹的文件中`/tmp/registry-ui/docker-compose.yml`。

执行：`docker-compose up -d`

查看日志：`docker-compose logs -f`

```yaml
version: '3.0'
services:
  registry:
    image: registry
    volumes:
      - ./registry-data:/var/lib/registry
  ui:
    image: joxit/docker-registry-ui:static
    ports:
      - 8080:80
    environment:
      - REGISTRY_TITLE=A私有镜像仓库
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
```

#### 6.1.3 配置Docker信任地址

我们的私服采用的是`http`协议，默认不被`Docker`信任，所以需要做一个配置：

```sh
# 打开要修改的文件
vi /etc/docker/daemon.json
# 添加内容：
{"insecure-registries":["http://192.168.150.101:8080"]}
# 重加载
systemctl daemon-reload
# 重启docker
systemctl restart docker
```

### 6.2 推送、拉取镜像

推送镜像到私有镜像服务必须先`tag`，步骤如下：

① 重新`tag`本地镜像，名称前缀为私有仓库的地址：`192.168.150.101:8080/`

 ```sh
docker tag nginx:latest 192.168.150.101:8080/nginx:1.0 
 ```

② 推送镜像

```sh
docker push 192.168.150.101:8080/nginx:1.0 
```

③ 拉取镜像

```sh
docker pull 192.168.150.101:8080/nginx:1.0
```