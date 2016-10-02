## 简介
blablabla...

##安装Docker

###安装
使用Ubuntu Kylin 16.04可以直接通过软件源安装
```
sudo apt-get install docker.io
```
安装完成后，通过`sudo docker info`来查看是否安装成功
```
lianli@lianli-VirtualBox:~$ sudo docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 1.11.2
Storage Driver: aufs
 Root Dir: /var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins: 
 Volume: local
 Network: null host bridge
Kernel Version: 4.4.0-21-generic
Operating System: Ubuntu Kylin 16.04 LTS
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 1.954 GiB
Name: lianli-VirtualBox
ID: YOO7:FM22:I56V:STOT:KBWM:B5XI:VBMW:B3RI:TXQM:5HEM:4ZY3:W3PU
Docker Root Dir: /var/lib/docker
Debug mode (client): false
Debug mode (server): false
Registry: https://index.docker.io/v1/
WARNING: No swap limit support
```
注意docker命令都需要带`sudo`，如果不想每次都敲，可以切换到root  
其余安装方式和条件参考官方文档https://docs.docker.com/

在Mac中，最新版本的OS X可以直接安装docker而无须通过Docker Toolbox

对于Windows，必须是安装Docker Toolbox（除了win10专业版）
![Docker Toolbox Install](https://github.com/swordrain/docker-notes/blob/master/image/docker_toolbox_install.png)  
其中Kitematic是一个镜像的GUI客户端  
![Kitematic](https://github.com/swordrain/docker-notes/blob/master/image/kitematic.png)  
Toolbox本质是通过一个微型的Linux虚拟机来运行Docker  
启动之后，能在virtualbox中看到一个运行中名为default的虚拟机
![default](https://github.com/swordrain/docker-notes/blob/master/image/virtual_box_default.png)  
显示该虚拟机  
![show](https://github.com/swordrain/docker-notes/blob/master/image/docker_toolbox_show.png)  

如果已经有virtual box和git bash，可以不安装，但注意最后生成的快捷方式可能不是已经安装的git bash的路径，而是以Docker Toolbox程序文件夹下的git bash作为启动入口。

如果安装的是Docker Toolbox，要在docker宿主启动时查看一下ip
```
docker-machine ip
```
![docker-machine-ip](https://github.com/swordrain/docker-notes/blob/master/image/docker-machine-ip.png)  
当今后docker启动web服务器镜像后，如果要查看效果，需要在外层通过该ip地址来访问的  

Linux机器也可以通过自动安装脚本来安装  
确保curl命令已经安装
```
curl htps://get.docker.com/ | sudo sh
```
![docker-machine-ip](https://github.com/swordrain/docker-notes/blob/master/image/docker-shell-install.png)  

###Docker守护进程
默认安装完成后，守护进程开始运行，也可以用`sudo docker daemon`命令启动  
守护进程监听`/var/run/docker.sock`这个Unix套接字文件，来获取来自客户端的Docker请求。如果系统中存在docker的group的话，Docker则会将该套接字文件的所有者设为该用户组。这样docker用户组的所有用户都可以直接运行Docker，而无须加sudo。（docker用户组用户权限等同于root）  

`-H`配置守护进程监听接口的方式
```
sudo docker daemon -H tcp://0.0.0.0:2375
```
可以通过设置`DOCKER_HOST`环境变量来持久化该配置
```
export DOCKER_HOST="tcp://0.0.0.0:2375"
```

```
#也可以指定套接字
sudo docker daemon -H unix://home/docker/docker.sock
#绑定多个
sudo docker daemon -H tcp://0.0.0.0:2375 -H unix://home/docker/docker.sock
```
```
#开启守护进程调试模式
sudo docker daemon -D
```
要让这些改动永久生效，需要编辑启动配置项。Ubuntu在`/etc/default/docker`，修改`DOCKER_OPTS`。其他Linux编辑`/usr/lib/systemd/system/docker.service`，修改`ExecStart`，或者`/etc/sysconfig/docker`文件。其他平台通过适当的init系统来管理Docker守护进程的启动配置

对于Linux  
使用`sudo status docker`来查看守护进程状态  
使用`sudo stop docker`或`sudo service docker stop`来停止docker  
使用`sudo start docker`或`sudo service docker start`来启动

###Docker用户界面  
*  Shipyard
*  DockerUI
*  Kitematic

##Docker入门

###Docker就绪
`sudo docker info`返回Docker的信息  
Docker是基于CS构架的，有一个docker程序，既能作为客户端也能作为服务器端。作为客户端，docker想Docker守护进程发送请求，再对返回的请求结果进行处理。  
![docker_info](https://github.com/swordrain/docker-notes/blob/master/image/docker_info.png)  
可以确定docker daemon守护进程是否运行  

###运行第一个容器
运行命令`sudo docker run -i -t ubuntu /bin/bash`  
![docker_run](https://github.com/swordrain/docker-notes/blob/master/image/docker_run.png)   
`-i`标志保证容器中STDIN开启  
`-t`告诉Docker为要创建的容器分配一个伪tty终端，若要在命令行总创建一个交互的容器，而不是一个后台服务的容器，这两个参数是基本的  
Docker会先检查本地是否有ubuntu镜像，如果没有，就去Docker Hub Registry下载。  
容器创建完毕后，就执行容器的`/bin/bash`命令，进入容器的shell。  

###使用第一个容器
可以在启动的容器中运行各种命令  
![docker_command](https://github.com/swordrain/docker-notes/blob/master/image/docker_command.png)   
输入`exit`可以结束docker容器运行，并退回到宿主机。  
使用`sudo docker ps -a`查看当前容器列表  
![docker_ps](https://github.com/swordrain/docker-notes/blob/master/image/docker_ps.png)   
如果不带参数`-a`，只能看到正在运行的容器。  

###容器命名
使用参数`--name`  
![docker_name](https://github.com/swordrain/docker-notes/blob/master/image/docker_name.png)  
容器的命名必须唯一，如果试图创建两个名称相同的容器会失败。如果要使用的容器名称已经存在，可以先用`docker rm`命令删除已有的同名容器，再创建。  

###重新启动已停止的容器
使用`docker start`命令启动已经停止的容器，可以指定名称或者id  
![docker_start](https://github.com/swordrain/docker-notes/blob/master/image/docker_start.png)

###attach到容器上
如果想在`docker start`启动的容器启动后打开shell，可以使用`docker attach`命令。
![docker_attach](https://github.com/swordrain/docker-notes/blob/master/image/docker_attach.png)  
注意，启动完后在terminal可能是空白，此时随意按下键盘即可让其显示。  
同样即可以指定名称，也可以指定id。  

###创建守护式容器
```
sudo docker run --name daemon_lianli -d ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"
```
使用了`-d`参数使容器在后台运行。
![docker_daemon](https://github.com/swordrain/docker-notes/blob/master/image/docker_daemon.png)  

###查看日志
```
sudo docker logs daemon_lianli
```
![docker_logs](https://github.com/swordrain/docker-notes/blob/master/image/docker_logs.png)  
也可以带上一些参数  
![docker_logs_parameter](https://github.com/swordrain/docker-notes/blob/master/image/docker_logs_parameter.png)  

###Docker日志驱动
用`--log-driver`控制Docker守护进程和容器所用的日志驱动，默认是`json-file`。如果用`syslog`会禁用`docker logs`命令，并将所有容器的日志输出重定向到Syslog。
```
sudo docker run --log-driver="syslog" --name daemon-lianli2 -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
查看logs  
```
lianli@lianli-VirtualBox:~$ sudo docker logs daemon_lianli2
"logs" command is supported only for "json-file" and "journald" logging drivers (got: syslog)
```
选用`none`会禁用所有容器日志。  

###查看容器内进程
```
sudo docker top daemon_lianli2
```
![docker_top](https://github.com/swordrain/docker-notes/blob/master/image/docker_top.png)  

###Docker统计信息
```
sudo docker stats daemon_lianli2 daemon_lianli
```
![docker_stats](https://github.com/swordrain/docker-notes/blob/master/image/docker_stats.png)  

###在容器内部运行进程
通过`docker exec`命令在容器内额外启动新进程，可以是后台任务也可以是交互式任务
```
sudo docker exec -d daemon_lianli2 touch /etc/new_config_file
sudo docker exec -t -i daemon_dave /bin/bash
```
![docker_exec](https://github.com/swordrain/docker-notes/blob/master/image/docker_exec.png)  

###停止守护式容器
还是使用`docker stop`命令

###自动重启容器
带上`--restart`，可以设置`always`，`on-failure`。当容器的退出代码为非0时，才会自动重启，可以指定次数如`--restart=on-failure:5`  
```
sudo docker run --restart=always --name daemon_lianli -d ubuntu /bin/sh -c "while true; do echo hello worldl sleep 1; done;"
```

###深入容器
```
sudo docker inspect lianli_ubuntu
```
![docker_inspect](https://github.com/swordrain/docker-notes/blob/master/image/docker_inspect.png)  
带上参数`-f`或`--format`查看选定结果  
![docker_inspect_parameter](https://github.com/swordrain/docker-notes/blob/master/image/docker_inspect_parameter.png)  

###删除容器
使用`docker rm`命令  
![docker_rm](https://github.com/swordrain/docker-notes/blob/master/image/docker_rm.png)  
删除所有容器
```
sudo docker rm `sudo docker ps -a -q`
```

##使用Docker镜像和仓库
###什么是Docker镜像
Docker镜像由文件系统叠加而成。最底端是一个引导文件系统即bootfs。当容器启动时，会移到内存中，引导文件系统被卸载以留出更多的内存供initrd磁盘镜像使用。  
Docker镜像第二层是root文件系统rootfs，位于引导文件系统之上。在Docker里，root文件系统永远只能只读。Docker利用union mount技术在root文件系统层上加载更多的只读文件系统。union mount指一次同时加载多个文件系统，但在外面看到只能看到一个文件系统。它将各层文件系统叠加到一起，最终的文件系统包含所有底层的文件和目录。
Docker将这样的文件系统称之为镜像。一个镜像可以放到另一个镜像的顶部。位于下面的镜像称为parent image，最底部的称为base image。最后当从一个镜像启动容器时，Docker会在该镜像的最顶层加载一个读写文件系统。 

 ————————————————  
|可写容器       |  
 ————————————————  
|镜像（Apache） |    
 ————————————————  
|镜像（emacs）  |    
 ————————————————  
|基础镜像Ubuntu |  
 ————————————————  
|引导文件系统   |  
 ————————————————  

当Docker第一次启动一个容器时，初始的读写层是空的。当文件系统发生改变时，这些变化都会应用到这一层上。这种机制称为copy on write。每个只读镜像层都是只读的，并且以后永远不会变化。当创建一个新容器时，Docker会构建出一个镜像栈，在栈的最顶端添加一个读写层。这个读写层再加上其下面的镜像层以及一些配置数据就构成了一个容器。  

###列出镜像
```
sudo docker images
```
![docker_images](https://github.com/swordrain/docker-notes/blob/master/image/docker_images.png)  
镜像从仓库下载下来。镜像保存在仓库中，仓库位于Registry中。默认的Registry是Docker运营的[Docker Hub](https://hub.docker.com/)  
![docker_hub](https://github.com/swordrain/docker-notes/blob/master/image/docker_hub.png)  

使用`sudo docker pull`拉取镜像  
```
sudo docker pull ubuntu:12.04
```
![docker_pull](https://github.com/swordrain/docker-notes/blob/master/image/docker_pull.png)  
拉取完后查看  
![docker_images_update](https://github.com/swordrain/docker-notes/blob/master/image/docker_images_update.png)  
`:12.04`是镜像的标签tag  

Docker Hub里有两种类型仓库，用户仓库和顶层仓库。顶层仓库由Docker内部管理。  
用户仓库命名由用户名和仓库名组成，如swordrain/first

###拉取镜像
在`docker run`启动镜像时，如果不存在，会从Docker Hub上下载，如果没有指定具体标签，会自动下载latest标签的镜像。
![docker_pull_result](https://github.com/swordrain/docker-notes/blob/master/image/docker_pull_result.png)  

###查找镜像
使用`sudo docker search keyword`

###构建镜像
使用`docker commit`命令  
使用`docker build`命令和`Dockerfile`文件  

####登陆到Docker Hub  
```
sudo docker login
```
![docker_login](https://github.com/swordrain/docker-notes/blob/master/image/docker_login.png)  

####用commit命令创建镜像
```
#创建新容器
sudo docker run --name lianli_commit -i -t ubuntu /bin/bash
#更新
apt-get update
#安装
apt-get install apache2
#退出
exit
#提交
sudo docker commit lianli_commit swordrain/new
#检查镜像
sudo docker images swordrain/new
#新的提交，有信息，作者和标签
sudo docker commit -m "A new custom image" -a "swordrain" lianli_commit swordrain/new:anotherNew
```
![docker_commit](https://github.com/swordrain/docker-notes/blob/master/image/docker_commit.png)  
查看镜像的详细信息
![docker_image_detail](https://github.com/swordrain/docker-notes/blob/master/image/docker_image_detail.png)  
运行新提交的镜像
![docker_run_new_commit](https://github.com/swordrain/docker-notes/blob/master/image/docker_run_new_commit.png)  

####用Dockerfile构建镜像
准备  
```
mkdir static_web
cd static_web
touch Dockerfile
```
`static_web`目录就是构建环境，Docker称此环境为上下文context或者构建上下文build context。
Dockerfile内容  
```
# Version: 0.0.1
FROM ubuntu:14.04
MAINTAINER swordrain "lianlitongji2002@163.com"
#参数不可少，不然执行到询问yes/no的时候默认是abort
RUN apt-get -yqq update && apt-get install -y nginx
RUN echo 'Hi, I am in your container' > /usr/share/nginx/html/index.html
EXPOSE 80
```
*  Docker从基础镜像运行一个容器
*  执行一条指令，对容器修改
*  执行类似docker commit的操作，提交一个新的镜像层
*  Docker再基于刚提交的镜像运行一个新容器
*  执行Dockerfile下一条指令，直到全部执行完毕

第一条指令必须是`FROM`，指定已经存在的基础镜像  
`MAINTAINER`标志所有者和联系方式  
默认`RUN`会在shell里使用命令包装器`/bin/sh -c`来执行。如果在不支持shell的平台或者不希望在shell中运行，可以使用`exec`格式的`RUN`指令
```
RUN ["apt-get", "install", "-y", "nginx"]
```
`EXPOSE`指令告诉Docker容器内的应用程序会使用容器的指定端口，Docker不会自动打开该端口，而是需要在使用`docker run`运行容器时来指定打开那些端口，也可以公开多个端口。  

####基于Dockerfile构建新镜像
```
#别少了最后的. 从当前目录寻找Dockerfile文件
sudo docker buold -t="swordrain/static_web" .
```

`-t`用来指定仓库和名称，也可以带上标签，如`sudo docker build -t="swordrain/static_web:v1" .`  
也可以指定从git仓库构建`sudo docker build -t="swordrain/static_web:v1" git@github.com:swordrain/docker-stattic_web`  

每一步都会有构建缓存，指令如果失败，如果重复执行，会直接基于之前正确构建的镜像缓存继续往下执行。  
可以基于中间某一步成功的步骤创建新容器
```
sudo docker run -t -i ff6011336327 /bin/bash
```
想要禁用缓存，使用`--no-cache`  
```
sudo docker build --nochche -t="swordrain/static_web" .
```

构建结果  
![docker_build_result](https://github.com/swordrain/docker-notes/blob/master/image/docker_build_result.png)  
使用`docker history`查看构建历史  
![docker_build_hisotry](https://github.com/swordrain/docker-notes/blob/master/image/docker_build_history.png)  

####从新镜像启动容器
```
sudo docker run -d -p 80 --name static_web swordrain/static_web nginx -g "daemon off;"
```
`-p`用来控制容器暴露给宿主机的端口，不指定的话随机在32768~61000中选择  
使用`sudo docker ps -l`查看端口映射
![port_mapping](https://github.com/swordrain/docker-notes/blob/master/image/port_mapping.png)  
使用`sudo docker port static_web port`查看映射情况  
![port_mapping2](https://github.com/swordrain/docker-notes/blob/master/image/port_mapping2.png)  

如果要绑定指定的端口或网络接口  
```
#将容器的80端口绑定到宿主机的8080端口
sudo docker run -d -p 8080:80 --name static_web swordrain/static_web nginx -g "daemon off;"
#绑定到宿主机的127.0.0.1IP的80端口上
sudo docker run -d -p 127.0.0.1:80:80 --name static_web swordrain/static_web nginx -g "daemon off;"
#绑定到宿主机的ip随机的端口
sudo docker run -d -p 127.0.0.1::80 --name static_web swordrain/static_web nginx -g "daemon off;"
```
如果使用`-P`表明使用Dockerfile中通过EXPOSE公开的所有端口到宿主机的随机端口

现在在宿主机上查看运行情况  
![port_mapping_result](https://github.com/swordrain/docker-notes/blob/master/image/port_mapping_result.png)  

####Dockerfile指令
**.dockerignore**
遵循Go语言规则
```
example/hello.txt
example/*.cpp
wo*
*.cpp
.git
.svn
```

**CMD**  
`CMD`用于指定容器启动时要运行的命令，类似于`RUN`。只是`RUN`指定镜像被构建时要运行的命令，`CMD`指定容器被启动时要运行的命令。和使用`docker run`命令启动容器时指定要运行的命令类似。  
```
#以下两个命令作用相同
sudo docker run -i -t swordrain/static_web /bin/true
CMD ["/bin/true"]
#也可以传递参数
CMD ["/bin/bash", "-l"]
```
`docker run`命令可以覆盖`CMD`指令。
```
CMD ["/bin/bash"]
#未指定命令，使用CMD指令中指定的命令
sudo docker run -t -i swordrain/test
#指定了命令，使用ps命令
sudo docker run -t -i swordrain/test /bin/ps
```
Dockerfile中只能指定一条`CMD`指令，如果指定了多条，以最后一条为准

**ENTRYPOINT**
`ENTRYPOINT`指定的命令不容易在启动容器时被覆盖。`docker run`命令行中指定的任何参数都会被当做参数再次传递给`ENTRYPOINT`指令中指定的命令。
```
ENTRYPOINT ["/usr/sbin/nginx"]

ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
```
如果启动  
```
sudo docker run -t -i swordrain/static_web -g "daemon off;"
```
`-g "deamon off;"`会被传递给用`ENTRYPOINT`指定的指令。  
也可以同时使用`ENTRYPOINT`和`CMD`来配合
```
ENTRYPOINT ["/usr/sbin/nginx"]
CMD ["-h"]
```
此时启动容器，如果`docker run`命令行有参数，则被传递给Nginx守护进程，如果没有参数，则`CMD`指令中的`-h`被传递给Nginx守护进程。  
`docker run`的`--entrypoint`标志可以覆盖`ENTRYPOINT`指令  

**WORKDIR**
用来从镜像创建一个新容器时，在容器内部设置一个工作目录，`ENTRYPOINT`和`/`或`CMD`指定的程序会在这个目录下执行  
```
WORKDIR /opt/webapp/db
RUN bundle install
WORKDIR /opt/webapp
ENTRYPOINT [ "rackup" ]
```
`docker run`的`-w`标志可以覆盖该指令

**ENV**
在镜像构建过程中设置环境变量，这个环境变量可以在后续的`RUN`中使用，环境变量被被创建的容器持久保存      
```
ENV RVM_PATH /home/rvm
#多个
ENV RVM_PATH=/home/rvm RVM_ARCHFLAGS="-arch i386"
```
`docker run`的`-e`标志可以覆盖该指令  

**USER**
指定镜像会以什么样的用户去运行
```
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```
`docker run`的`-u`标志可以覆盖该指令  

**VOLUME**
用来向基于镜像创建的容器添加卷。一个卷是可以存在于一个或多个容器内的特定的目录，这个目录可以绕过联合文件系统，并提供如下共享数据或者对数据进行持久化的功能  
* 卷可以在容器间共享和重用
* 一个容器可以不是必须和其他容器共享卷
* 对卷的修改是可以立时生效的
* 对卷的修改不会对更新镜像产生影响
* 卷会一直存在直到没有任何容器再使用它
 卷功能让我们可以将数据（如源码）、数据库或其他内容添加到镜像而不是将这些内容提交到镜像中，并且允许我们在多个容器间共享这些内容。我们可以利用此功能来测试容器和内部的应用程序代码，管理日志，或者处理容器内部的数据库。
```
VOLUMN ["/opt/project"]
VOLUMN ["/opt/project", "/data"]
```

**ADD**
用来将构建环境下的文件和目录复制到镜像中
```
ADD software.lic /opt/application/software.lic
ADD http://wordpress.org/latest.zip /root/wordpress.zip
```
目的地址末尾的字符是否是`/`决定了是目录还是文件  
如果将一个归档文件指定为源文件，在目的地址中会自动解开  
如果目的地址不存在，Docker会自动创建这个路径，目录和文件的模式为0755，UID和GID都是0

**COPY**
类似于`ADD`，`COPY`只关心在构建上下文中复制文件，不会去做文件提取和解压的工作  
```
COPY conf.d/ /etc/appache2/
```

**LABEL**
为Docker镜像添加元数据。元数据以键值对的形式展现  
```
LABEL version="1.0"
LABEL locatio="New York" type="Data Center" role="Web Server"
```
通过`sudo docker inspect`可以查看LABEL

**STOPSINGAL**
用来设置停止容器时发送什么系统调用信号给容器  

**ARG**
用来定义在`docker build`命令运行时传递给构建运行时的变量，然后在构建时使用`--build-arg`标志即可。
```
ARG build
ARG webapp_user=user
```
```
sudo docker build --build-arg build=1234 -t swordrain/webapp .
```
Docker预定义了一组`ARG`变量
```
 HTTP_PROXY
 http_proxy
 HTTPS_PROXY
 https_proxy
 FTP_PROXY
 ftp_proxy
 NO_PROXY
 no_proxy
```

**ONBUILD**
为镜像添加触发器。当一个镜像被用作其他镜像的基础镜像时，触发器被执行  
触发器会在构建过程中插入新指令，紧跟在`FROM`之后  
```
ONBUILD ADD . /app/src
ONBUILD RUN cd /app/src && make
```
也可以通过`docker inspect`来查看已设置的ONBUILD

###将镜像推送到Docker Hub
先要通过`docker login`登陆到Docker Hub  
然后执行
```
sudo docker push swordrain/static_web
```
![docker_push](https://github.com/swordrain/docker-notes/blob/master/image/docker_push.png)  
![docker_push_result](https://github.com/swordrain/docker-notes/blob/master/image/docker_push_result.png)  

**自动构建**  
Docker Hub可以链接GitHub或BitBucker中的Dockerfile文件，像这个代码仓库提交代码时，会触发一次镜像构建活动并创建一个新镜像  
![automatic_build](https://github.com/swordrain/docker-notes/blob/master/image/automatic_build.png)  

###删除镜像
```
sudo docker rmi swordrain/static_web
sudo docker rmi swordrain/static_web swordrain/puppetmaster
```
此时删除的仅是本地镜像  
删除所有镜像  
```
sudo docker rmi `docker images -a -q`
```

###运行自己的Docker Registry


##在测试中使用Docker
###使用Docker测试静态网站
```
mkdir sample
cd sample
touch Dockerfile
```
```
#获取Nginx配置文件
mkdir nginx && cd nginx
wget https://raw.githubusercontent.com/jamtur01/dockerbook-code/master/code/5/sample/nginx/global.conf
wget https://raw.githubusercontent.com/jamtur01/dockerbook-code/master/code/5/sample/nginx/nginx.conf
cd ..
```
```
#Dockerfile
FROM ubuntu:14.04
MAINTAINER swordrain "lianlitongji2002@163.com"
ENV REFRESHED_AT 2016-08-22
RUN apt-get -yqq update && apt-get -yqq install nginx
RUN mkdir -p /var/www/html/website
ADD nginx/global.conf /etc/nginx/conf.d
ADD nginx/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```
```
#global.conf
server {
        listen          0.0.0.0:80;
        server_name     _;

        root            /var/www/html/website;
        index           index.html index.htm;

        access_log      /var/log/nginx/default_access.log;
        error_log       /var/log/nginx/default_error.log;
}
```
```
#nginx.conf
user www-data;
worker_processes 4;
pid /run/nginx.pid;
daemon off;

events {  }

http {
  sendfile on;
  tcp_nopush on;
  tcp_nodelay on;
  keepalive_timeout 65;
  types_hash_max_size 2048;
  include /etc/nginx/mime.types;
  default_type application/octet-stream;
  access_log /var/log/nginx/access.log;
  error_log /var/log/nginx/error.log;
  gzip on;
  gzip_disable "msie6";
  include /etc/nginx/conf.d/*.conf;
}
```
开始构建`sudo docker build -t swordrain/nginx .`  
显示构建历史`sudo docker history swordrain/nginx`  
![nginx_build_history](https://github.com/swordrain/docker-notes/blob/master/image/nginx_build_history.png)  

下载网站  
```
mkdir website && cd website
wget https://raw.githubusercontent.com/jamtur01/dockerbook-code/master/code/5/sample/website/index.html
cd ..
```
运行  
```
sudo docker run -d -p 80 --name website -v $PWD/website:/var/www/html/website swordrain/nginx nginx
```
查看一下端口映射`sudo docker ps -l`  
在宿主机上打开映射的端口  
![nginx_result](https://github.com/swordrain/docker-notes/blob/master/image/nginx_result.png)  
打开宿主机上`website`目录下的`index.html`文件修改，再次浏览查看结果  
![nginx_result2](https://github.com/swordrain/docker-notes/blob/master/image/nginx_result2.png)   

##Docker监控##
Graphite，可以将Carbon, Graphite Web, Crafana, Elasticsearch, Diamone组合到Graphite，并用Docker容器运行。

