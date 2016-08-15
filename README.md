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

对于Windows，必须是安装Docker Toolbox  
![Docker Toolbox Install](https://github.com/swordrain/docker-notes/blob/master/image/docker_toolbox_install.png)  
其中Kitematic是一个镜像的GUI客户端  
![Kitematic](https://github.com/swordrain/docker-notes/blob/master/image/kitematic.png)  
Toolbox本质是通过一个微型的Linux虚拟机来运行Docker  

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

###运行第一个容器
运行命令`sudo docker run -i -t ubuntu /bin/bash`




