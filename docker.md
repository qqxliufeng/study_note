## Docker学习笔记

### 1. docker是什么

**docker是一款软件，采用c/s架构的方式，是对linux底层虚拟化容器技术(LXC)的进一步包装，运行在docker上的容器相互之间隔离**

### 2. Docker 三要素

+ 镜像
+ 容器
+ 仓库

### 3. 镜像

​	**一个镜像是由名称和tag组成，如：mysql:8.0，如果不写tag，则默认的就是latest。镜像就好像一个千层饼一样是由一层层的其它基本镜像组合而来，只对外暴露最外层的层**

+ 创建镜像的四种方式

  1. 通过docker pull 的方式，从docker hub 或者 私有仓库中获取

  2. 通过容器实例创建

  3. 通过Dockerfile描述文件生成

  4. 通过文件中加载，命令

     ```shell
     docker save -o {文件路径} {镜像名称:tag} //把镜像生成一个文件保存，可用于多个服务器之前的迁移
     docker load -i {文件路径}  //从一个文件中加载生成一个image镜像
     ```

+ 常用的命令

  1. 获取本地所有的images

     docker images

     docker images -q (只显示镜像的id)

     ```shell
     > docker images
     test-mynginx             v1                  55eb78023dfc        5 hours ago         138MB
     mynginx-test             v2                  8882432411df        7 hours ago         138MB
     596484713/mynginx        v1                  3079ccb0429a        23 hours ago        138MB
     mynginx                  v1                  3079ccb0429a        23 hours ago        138MB
     ```

  2. 删除镜像

     docker rmi {镜像的id}

     docker rmi $(docker images -q) 删除所有的镜像

     ```shell
     docker rmi 55eb78023dfc
     ```

  3. 查看镜像的详细信息

     docker inspect {镜像的id}

     ```shell
     docker inspect 55eb78023dfc
     ```

### 4. 容器

**容器是由镜像创建而来，就好比java中类与实例对象的关系，一个类可以创建很多个实例对象，但是镜像也可以生成很多个容器**

+ 通过镜像启动容器

  docker run [ -it | -d ]  [--name 容器名称] 镜像名称:tag

  -it 是以交互式的方式启动容器

  -d 是以后台的方式启动容器

+ 查看所有的镜像实例

  ```shell
  docker ps
  ```

+ 删除某个容器实例

  ```shell
  docker rm {容器id}
  ```

+ 停止某个容器实例

  ```shell
  docker stop {容器id}
  ```

+ 重新启动某个容器实例

  ```shell
  docker restart {容器id}
  ```

+ 通过实例容器生成镜像

  ```shell
  docker commit -a {作者信息} -m '提交描述' 容器id 镜像名称:tag
  ```

### 5. 数据卷

数据卷是容器和宿主机交换\共享数据的一种方式，通过此方式可以方便的把宿主机上的数据传输到容器中

### 6. Dockerfile文件

可以通过dockerfile文件生成一个镜像，文件类似于一个shell脚本，生成一个符合我们需求的镜像

**Dockerfile常用的命令：**

+ FROM  基础镜像，依赖于哪个镜像，最基本的依赖是：scratch
+ ENV  设置一些环境变量，如设置一下JAVA_HOME 等
+ WORKDIR  进入容器中默认的路径，如设置成 /usr/local，则进入容器后目录就是：/usr/local
+ ADD  把宿主机的上文件添加到容器中，如果是压缩文件可以解压
+ COPY 把宿主机的上文件添加到容器中，不可以解压文件，仅仅是copy
+ EXPOSE  对外暴露的端口，对一些服务软件有用，如nginx 对外暴露 80， tomcat 对外暴露 8080
+ RUN   执行一些linux命令（此命令会在创建镜像的时候执行）
+ CMD 执行一些linux命令 （此命令会在创建容器的时候执行）

记一次简单的`Dockerfile`文件的内容，主要是安装php运行环境并且带着`phpredis`客户端的操作，如下：

```dockerfile
FROM php:7.4-fpm
RUN apt-get update && apt-get install vim -y && apt-get install net-tools -y //安装了一些基本常用的工具（vim, ifconig等）
RUN curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/5.2.2.tar.gz
RUN tar xfz /tmp/redis.tar.gz
RUN rm -r /tmp/redis.tar.gz
RUN mkdir -p /usr/src/php/ext
RUN mv phpredis-5.2.2 /usr/src/php/ext/redis
RUN docker-php-ext-install redis
```

这样通过

```shell
docker build -t xxx(镜像名) . 
```

生成的镜像就会有`php`对`redis`操作的能力了，相当于安装了`php`对`redis`的扩展

