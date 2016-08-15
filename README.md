## 简介
blablabla...

##安装Docker
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

如果已经有virtual box和git bash，可以不安装，但注意最后生成的快捷方式可能不是已经安装的git bash的路径，而是以Docker Toolbox程序文件夹下的git bash作为启动入口。

如果安装的是Docker Toolbox，要在docker宿主启动时查看一下ip
```
docker-machine ip
```
![docker-machine-ip](https://github.com/swordrain/docker-notes/blob/master/image/docker-machine-ip.png)  
当今后docker启动web服务器镜像后，如果要查看效果，需要在外层通过该ip地址来访问的  

Linux机器也可以通过自动安装脚本来安装  
确保curl命令已经安装

