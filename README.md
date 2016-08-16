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
#修改容器
mkdir test
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

