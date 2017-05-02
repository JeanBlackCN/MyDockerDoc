[TOC]
## 1 CentOS7 源安装
### 1.1 向目标主机添加源文件
``cd /etc/yum.repos.d/``

``ls``
查看``yum.repos.d``目录下的无效源文件，以``mv [filename] [filename].bak``的方式注释，并新建可用源文件如下：

``vim CentOS-Base.repo`` 

写入源文件  

```
#CentOS-Base.repo
#The mirror system uses the connecting IP address of the client and the
#update status of each mirror to pick mirrors that are updated to and
#geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
[base]
name=CentOS-$releasever - Base
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
baseurl=http://172.21.97.80/yum/centos/7/os/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates 
[updates]
name=CentOS-$releasever - Updates
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
baseurl=http://172.21.97.80/yum/centos/7/updates/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
baseurl=http://172.21.97.80/yum/centos/7/extras/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

执行``yum update -y``命令，update更新完成后安装所需软件。

## 2 Docker安装
### 2.1 Linux下面安装docker
#### 2.1.1 Ubuntu Precise 12.04 (LTS) (64-bit)下面安装docker

* 内核要求：

由于LXC的一个bug，Docker在3.8内核下面运行最佳。Ubuntu的Precise版本内置的是3.2版本的内核，因此我们首先需要升级内核。安装下面的步骤可以升级到3.8内核，并内置AUFS的支持。同时还包括了通用头文件，这样我们就可以激活依赖于这些头文件的包，比如ZFS，VirtualBox的增强功能包。
```
# install the backported kernel
sudo apt-get update
sudo apt-get install linux-image-generic-lts-raring linux-headers-generic-lts-raring

# reboot
sudo reboot
```
* 安装Docker：
Docker有deb格式的安装包，安装起来非常的容易。首先添加Docker库的密钥。
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
```
然后把Docker的库添加到apt的源列表中，更新并安装lxc-docker包。
```
sudo sh -c "echo deb http://get.docker.io/ubuntu docker main\
> /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install lxc-docker
```

安装过程中会有一个警告信息，输入"yes"继续安装即可。安装成功之后，可以下载ubuntu镜像并启动一个镜像来验证安装是否正常。
``sudo docker run -i -t ubuntu /bin/bash``
成功运行之后，输入exit退出即可。
#### 2.1.2 Ubuntu Raring 13.04 和 Saucy 13.10 (64 bit)下面安装docker
* 激活AUFS文件系统支持

Ubuntu的Raring版本内置了3.8版本的内核，因此我们不需要安装AUFS系统。但是并不是所有的系统都激活了AUFS系统的支持。Docker 0.7版本中AUFS的支持是可选项，以驱动的方式存在，如果可能的话，我们还是建议使用AUFS系统。使用下面的命令来确保AUFS安装成功。
```
sudo apt-get update
sudo apt-get install linux-image-extra-`uname -r`
```

* 安装

Docker有Debian包，所以安装起来非常的方便。

* 警告：请注意，从0.6版本开始安装步骤已经发生了变化，如果你是从比较早的版本升级上来，还需要执行下面的命令。

首先把Docker库的密钥添加到本地。
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
```
把Docker库添加到apt的源列表中，执行update命令，然后安装lxc-docker包。
```
sudo sh -c "echo deb http://get.docker.io/ubuntu docker main\
> /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install lxc-docker
```

现在来确认安装是否成功，可以下载ubuntu镜像并启动一个容器。
``sudo docker run -i -t ubuntu /bin/bash ``

#### 2.1.3 Ubuntu Trusty 14.04 (LTS) 下面安装docker &&  Ubuntu Vivid 15.04 下面安装docker
docker的发展非常迅速，apt源的更新往往比较滞后。所以docker官网推荐的安装方式都是下载docker安装脚本安装。

* 依赖关系：

Ubuntu 14.04版本无需安装额外的依赖包，可以直接安装。
安装步骤：

使用管理员帐号登录ubuntu 14.04系统，保证该管理有root权限，或者可以执行sudo命令。
检查curl包有没有安装。
``$ which curl``
如果curl没有安装的话，更新apt源之后，安装curl包。
``$ sudo apt-get update $ sudo apt-get install curl``
获得最新的docker安装包。
``$ curl -sSL https://get.docker.com/ | sh ``
shell会提示你输入sudo的密码，然后开始执行安装过程。
确认Docker是否安装成功。
``$ sudo docker run hello-world``
这个命令会下载一个测试用的镜像并启动一个容器运行它。

#### 2.1.4 Ubuntu Xenial 16.04 (LTS)下面安装docker

* Recommended extra packages

You need curl if you don’t have it. Unless you have a strong reason not to, install the linux-image-extra-* packages, which allow Docker to use the aufs storage drivers. This applies to all versions of Ubuntu.
```
$ sudo apt-get update

$ sudo apt-get install curl \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual
```
##### Install using the repository
Before you install Docker for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install, update, or downgrade Docker from the repository.

一、Set up the repository

1.Install packages to allow apt to use a repository over HTTPS:

```
$ sudo apt-get install apt-transport-https \
                       ca-certificates
```

2.Add Docker’s official GPG key:
``$ curl -fsSL https://yum.dockerproject.org/gpg | sudo apt-key add -``

Note: The URL is correct, even for Linux distributions that use APT.
Verify that the key ID is 58118E89F3A912897C070ADBF76221572C52609D.
```
$ apt-key fingerprint 58118E89F3A912897C070ADBF76221572C52609D

  pub   4096R/2C52609D 2015-07-14
        Key fingerprint = 5811 8E89 F3A9 1289 7C07  0ADB F762 2157 2C52 609D
  uid                  Docker Release Tool (releasedocker) <docker@docker.com>
```
3.Use the following command to set up the stable repository. To also enable the testing repository, add the words testing after main on the last line. Do not use these unstable repositories on production systems or for non-testing workloads.
```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository \
       "deb https://apt.dockerproject.org/repo/ \
       ubuntu-$(lsb_release -cs) \
       main"
```
To disable the testing repository, you can edit /etc/apt/sources.list and remove the word testing from the appropriate line in the file.

Install Docker

1.Update the apt package index.

``$ sudo apt-get update``

2.Install the latest version of Docker, or go to the next step to install a specific version. Any existing installation of Docker is replaced.
Use this command to install the latest version of Docker:

``$ sudo apt-get -y install docker-engine``

Warning: If you have both stable and unstable repositories enabled, installing or updating without specifying a version in the apt-get install or apt-get update command will always install the highest possible version, which will almost certainly be an unstable one.

3.On production systems, you should install a specific version of Docker instead of always using the latest. This output is truncated. List the available versions.
```
$ apt-cache madison docker-engine

docker-engine | 1.13.0-0~xenial | https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
docker-engine | 1.12.3-0~xenial | https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
docker-engine | 1.12.2-0~xenial | https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
docker-engine | 1.12.1-0~xenial | https://apt.dockerproject.org/repo ubuntu-xenial/main amd64 Packages
```

The contents of the list depend upon which repositories are enabled, and will be specific to your version of Ubuntu (indicated by the xenial suffix on the version, in this example). Choose a specific version to install. The second column is the version string. The third column is the repository name, which indicates which repository the package is from and by extension its stability level. To install a specific version, append the version string to the package name and separate them by an equals sign (=):

``$ sudo apt-get -y install docker-engine=<VERSION_STRING>``

The Docker daemon starts automatically.
4.Verify that docker is installed correctly by running the hello-world image.

``$ sudo docker run hello-world``

This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.
Docker is installed and running. You need to use sudo to run Docker commands. Continue to Linux postinstall to allow non-privileged users to run Docker commands and for other optional configuration steps.

* Upgrade Docker
To upgrade Docker, first run sudo apt-get update, then follow the installation instructions, choosing the new version you want to install.

##### Install from a package
If you cannot use Docker’s repository to install Docker, you can download the .deb file for your release and install it manually. You will need to download a new file each time you want to upgrade Docker.

1.Go to https://apt.dockerproject.org/repo/pool/main/d/docker-engine/ and download the .deb file for the Docker version you want to install and for your version of Ubuntu.

Note: To install a testing version, change the word main in the URL to testing. Do not use unstable versions of Docker in production or for non-testing workloads.

2.Install Docker, changing the path below to the path where you downloaded the Docker package.
``$ sudo dpkg -i /path/to/package.deb``
The Docker daemon starts automatically.
3.Verify that docker is installed correctly by running the hello-world image.
``$ sudo docker run hello-world``
This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.
Docker is installed and running. You need to use sudo to run Docker commands. Continue to Post-installation steps for Linux to allow non-privileged users to run Docker commands and for other optional configuration steps.

* Upgrade Docker
To upgrade Docker, download the newer package file and repeat the installation procedure, pointing to the new file.



#### 2.1.5 红帽RHEL安装docker依赖性检查
Docker目前可以在红帽企业版7(Red Hat Enterprise Linux 7)版本下面安装。
依赖性检查：

Docker需要一个64位系统的红帽系统，内核的版本必须大于3.10。可以用下面的命令来检查是否满足docker的要求。
``$ uname -r 3.10.0-229.el7.x86_64 ``
如果上述的依赖满足的话，还是推荐您全面地更新红帽系统，以保证内核相应的bug都得到修复。

#### 2.1.6 红帽RHEL安装docker容器引擎
目前红帽RHEL系统下面安装docker可以有两种方式：一种是使用curl获得docker的安装脚本进行安装，还有一种是使用yum包管理器来安装docker。

* 一、 使用安装脚本安装。

* 备注：你可以按照同样的步骤在CentOS系统下面安装docker。

使用一个有sudo权限的帐号登录红帽系统。

1.更新现有的yum包。

``$ sudo yum update ``

2.执行docker安装脚本。

``$ curl -sSL https://get.docker.com/ | sh ``

3.启动docker服务。

``$ sudo service docker start``

4.确认docker安装成功。

```
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from hello-world
a8219747be10: Pull complete 91c95931e552: Already exists 
hello-world:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security. Digest: sha256:aa03e5d0d5553b4c3473e89c8619cf79df368babd1.7.1cf5daeb82aab55838d
Status: Downloaded newer image for hello-world:latest
Hello from Docker.
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps: 1. The Docker client contacted the Docker daemon. 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (Assuming it was not already locally available.) 3. The Docker daemon created a new container from that image which runs the executable that produces the output you are currently reading. 4. The Docker daemon streamed that output to the Docker client, which sent it to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
For more examples and ideas, visit: http://docs.docker.com/userguide/
```

* 二、使用yum包安装

使用有sudo权限的帐号登录系统。
1.更新yum包。
``$ sudo yum update ``
2.添加docker源。

```
$ cat >/etc/yum.repos.d/docker.repo <<-EOF
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7 enabled=1 gpgcheck=1 gpgkey=https://yum.dockerproject.org/gpg EOF
```
3.使用yum命令安装docker。
``$ sudo yum install docker-engine ``
4.启动docker服务。
``$ sudo service docker start``
5.确认docker是否安装成功。
``$ sudo docker run hello-world``

#### 2.1.7 不使用sudo命令执行docker

* 为什么需要创建docker用户组？

Docker守候进程绑定的是一个unix socket，而不是TCP端口。这个套接字默认的属主是root，其他是用户可以使用sudo命令来访问这个套接字文件。因为这个原因，docker服务进程都是以root帐号的身份运行的。
为了避免每次运行docker命令的时候都需要输入sudo，可以创建一个docker用户组，并把相应的用户添加到这个分组里面。当docker进程启动的时候，会设置该套接字可以被docker这个分组的用户读写。这样只要是在docker这个组里面的用户就可以直接执行docker命令了。

* 警告：该dockergroup等同于root帐号，具体的详情可以参考这篇文章：Docker Daemon Attack Surface.

操作步骤：
1.使用有sudo权限的帐号登录系统。
2.创建docker分组，并将相应的用户添加到这个分组里面。
``sudo usermod -aG docker your_username``
3.退出，然后重新登录，以便让权限生效。
4.确认你可以直接运行docker命令。
``$ docker run hello-world``

#### 2.1.8 红帽RHEL下面设置docker服务自动启动

#### 2.1.9 红帽RHEL如何删除docker
可以使用yum来删除docker。
1.列出docker包的具体的名字。
```
$ yum list installed | grep docker
yum list installed | grep docker
docker-engine.x86_64 1.7.1-0.1.el7
```
2.删除docker。
``$ sudo yum -y remove docker-engine.x86_64 ``

* 备注：该命令只是删除docker运行环境，并不会删除镜像，容器，卷文件，以及用户创建的配置文件。

3.清除镜像和容器文件。
``$ rm -rf /var/lib/docker``
4.手工查找并删除用户创建的配置文件。

#### 2.1.10 Debian 8(Jessie )下面如何安装docker
Debian也是使用非常广泛的系统。这篇文章讲述如何在debian 8 下面安装docker运行环境。

Debian 8 自带了3.16的内核，已经满足了docker运行的要求。
但是因为安全方面的原因，docker.io包并没有放在debian的stable源里面，而是放在了backports源里面。

打破沙锅问到底的朋友，可以参考这两篇文章：
为什么从jessie源里面移除docker.io包:https://lists.debian.org/debian-release/2015/03/msg00685.html(因为go语言包版本的问题)
Debian backports的介绍和使用: http://backports.debian.org/Instructions/

* 一、安装docker.io包之前，需要先设置使用backports源

编辑/etc/apt/sources.list文件，加入下面这一句：
``deb http://http.debian.net/debian jessie-backports main``
然后执行apt-get update命令更新源。

* 二、安装docker.io包

确保已经按照第一步的操作步骤添加了backports源，然后执行下面的命令。
``$ sudo apt-get update $ sudo apt-get install docker.io``
确认docker运行是否正常。
``$ sudo docker run --rm hello-world ``

* 三、Debian 8如何删除docker?

Debian系统下面删除docker，要按照下面几个步骤来删除。
1. 使用purge命令清除docker-io包。
``$ sudo apt-get purge docker-io``
或者用autoremove命令将不再使用的依赖的包删除掉。
``$ sudo apt-get autoremove --purge docker-io ``
需要注意的是上面的命令只是删除了docker.io包，并不会删除下载的镜像，产生的容器文件，卷，已经用户创建的配置文件。如果你觉得不爽，可以用下面的命令干掉它们：
``$ rm -rf /var/lib/docker``
然后手工删除创建的配置文件。
* 备注：docker这个名字已经被占用了，所以debian下面的docker的包的名字是docker.io。

#### 2.1.11 Debian 7(Wheezy)下面如何安装docker

因为bug #407的原因，docker要求linux内核必须大于等于3.8。很不幸的是debian7(wheezy)版本自带的是3.2版本。
怎么办呢？还是要使用backports源。wheezy-backports支持3.16版本的内核，可以满足docker的要求。
一、设置backports源

将下面这句话添加到apt的源文件中。
``deb http://http.debian.net/debian wheezy-backports main``
二、安装docker

从backports源安装新版本的内核，注意命令中的-t wheezy-backports选项。
``$ sudo apt-get update $ sudo apt-get install -t wheezy-backports linux-image-amd64 ``
重启机器以启用新的内核。
使用docker的安装脚本安装docker。
``curl -sSL https://get.docker.com/ | sh``
三、删除

删除docker包
``$ sudo apt-get purge docker-engine``
同时删除不再使用的依赖包。
``$ sudo apt-get autoremove --purge docker-engine``
使用下面的命令删除镜像文件，容器文件，卷文件。
``$ rm -rf /var/lib/docker``
然后手工删除用户创建的配置文件。

### 2.2 Windows下面安装docker
详细见官方文档[https://docs.docker.com/docker-for-windows/]

### 2.3 Mac OS X 下面安装docker
详细见官方文档[https://docs.docker.com/docker-for-mac/]

## 3 配置docker网桥

Docker 服务默认会创建一个 docker0 网桥，它在内核层连通了其他的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。
用户也可以指定网桥来连接各个容器，步骤如下：

### 3.1 安装工具

```
$ sudo apt-get install bridge-utils
```

然后可以用``brctl show``来查看当前网桥信息，可以看到目前只有一个``docker0``

``brctl show``

```
bridge name bridge id         STP enabled interfaces
docker0     8000.56847afe9799 no          veth59c4517 
```

### 3.2  然后创建一个网桥 br0

```
$ sudo brctl addbr br0
$ sudo ip addr add 192.168.66.1/24 dev br0
$ sudo ip link set dev br0 up
```

添加后可以用``brctl show``来查看
```
$ brctl show
```
```
bridge name bridge id         STP enabled interfaces
br0         8000.000000000000 no          veth59c4517
docker0     8000.56847afe9799 no 
```
### 3.3 切换网桥
编辑``/etc/default/docker``文件，添加如下Docker参数，是Docker默认使用上面新添加的网桥
```
DOCKER_OPTS="-b=br0"
```
或者，如果你是显式启动Docker，可以在启动命令中加入``-b=br0``，例如``dockerd -b=br0 ``等

### 3.4 重启docker服务
```
$ sudo service docker restart
```
此时，新建一个容器，可以看到它已经桥接到了 br0 上了。

### 3.5 删除网桥
最后，记得删除``docker0``网桥,不然可能会引起向外访问地址重定向到docker容器内网中,出现无法访问等一系列问题。
```
$ sudo ip link set dev docker0 down
$ sudo brctl delbr docker0
```
## 4 搭建Docker私有镜像库
和Mavan的管理一样，Dockers不仅提供了一个中央仓库，同时也允许我们使用registry搭建本地私有仓库。

使用私有仓库有许多优点：

* 节省网络带宽，针对于每个镜像不用每个人都去中央仓库上面去下载，只需要从私有仓库中下载即可；
* 提供镜像资源利用，针对于公司内部使用的镜像，推送到本地的私有仓库中，以供公司内部相关人员使用。

接下来我们就大致说一下如何在本地搭建私有仓库。

目前Docker Registry已经升级到了v2，最新版的Docker已不再支持v1。Registry v2使用Go语言编写，在性能和安全性上做了很多优化，重新设计了镜像的存储格式。此文档是在v1的基础上写的，如果需要安装registry v2，只需下载registry:2.2即可，或者可以下载后面的安装脚本运行安装。

### 4.1 镜像拉取或制作
* 首先下载registry镜像

```
$ sudo docker pull registry
```
* 下载完之后我们通过该镜像启动一个容器

```
$ sudo docker run -d -p 5000:5000 registry
```
默认情况下，会将仓库存放于容器内的/tmp/registry目录下，这样如果容器被删除，则存放于容器中的镜像也会丢失，所以我们一般情况下会指定本地一个目录挂载到容器内的/tmp/registry下，如下：

V1:
```
$ sudo docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry 
```
V2:
```
$ sudo docker run -d -p 5000:5000 --restart=always -v /opt/registry:/var/lib/registry
```
* Registry V2.2的安装脚本如下：

```

#!/bin/bash
# Description: create a private registry v2.2
# Version: 0.2
#
# Author: wangtao 479021795@qq.com
# Date: 2015/10/29

set -o xtrace

if [[ $UID -ne 0 ]]; then
    echo "Not root user. Please run as root."
    exit 0
fi

# Install Docker if not
docker -v
if [[ $? -ne 0 ]]; then
    echo "Please install Docker first."
    exit 0
fi

REGISTRY_VERSION=2.2

# Download registry image v2.2
docker pull registry:${REGISTRY_VERSION}

# Start registry container
mkdir /opt/registry
docker run -d -p 5000:5000 --restart=always -v /opt/registry:/var/lib/registry --name registry registry:${REGISTRY_VERSION}

```
### 4.2 推送镜像
首先将从DockerHub下载的busybox打一下tag：
```
$ sudo docker tag busybox 10.1.65.227:5000/base/busybox
```
接下来把打了tag的镜像上传到私有仓库。
```
$ sudo docker push 10.1.65.227:5000/base/busybox
```
此时，可以看到push失败，具体错误如下：
```
2015/01/05 11:01:17 Error: Invalid registry endpoint https://192.168.112.136:5000/v1/: Get https://10.1.65.227:5000/v1/_ping: dial tcp 10.1.65.227:5000: connection refused. If this private registry supports only HTTP or HTTPS with an unknown CA certificate, please add `--insecure-registry 10.1.65.227:5000` to the daemon's arguments. In the case of HTTPS, if you have access to the registry's CA certificate, no need for the flag; simply place the CA certificate at /etc/docker/certs.d/192.168.112.136:5000/ca.crt 
```

因为Docker从1.3.X之后，与docker registry交互默认使用的是https，然而此处搭建的私有仓库只提供http服务，所以当与私有仓库交互时就会报上面的错误。为了解决这个问题需要在启动docker server时增加启动参数为默认使用http访问。修改docker启动配置文件（此处是修改132机器的配置）Ubuntu下配置文件地址为：``/etc/default/docker``或者``/etc/init/docker.conf``、``/etc/init.d/docker``，在其中``DOCKER_OPTS``或者启动参数位置增加``–insecure-registry 192.168.112.136:5000`` ：
```
$ sudo vi /etc/init/docker.conf
```
添加位置如下
```java
script
    # modify these in /etc/default/$UPSTART_JOB (/etc/default/docker)
	DOCKERD=/usr/bin/dockerd
	DOCKER_OPTS= 
	if [ -f /etc/default/$UPSTART_JOB ]; then
		. /etc/default/$UPSTART_JOB
	fi
	exec "$DOCKERD" $DOCKER_OPTS --raw-logs
end script
```
```
$ sudo vi /etc/init.d/docker
```
```java
export PATH=/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin

BASE=docker

# modify these in /etc/default/$BASE (/etc/default/docker)
DOCKERD=/usr/bin/dockerd
# This is the pid file managed by docker itself
DOCKER_PIDFILE=/var/run/$BASE.pid
# This is the pid file created/managed by start-stop-daemon
DOCKER_SSD_PIDFILE=/var/run/$BASE-ssd.pid
DOCKER_LOGFILE=/var/log/$BASE.log

DOCKER_OPTS="-b=bridge0 --insecure-registry=172.18.0.0/16 --default-ulimit nofile=65535:65535  --insecure-registry 10.1.65.66:8080 --insecure-registry 10.10.15.168:5000 --insecure-registry 10.1.65.227:5000 --registry-mirror=http://6e512fcf.m.daocloud.io --selinux-enabled -H 0.0.0.0:2375 -H unix:///var/run/docker.sock  --insecure-registry 172.18.11.33:5000 --insecure-registry 10.1.64.83:5000  --insecure-registry 10.1.65.66:5000 --insecure-registry 10.1.64.84:5000  --insecure-registry 172.18.14.11:5000"
DOCKER_DESC="Docker"
```
若服务器Linux发行版本为CentOS，则做如下操作：

```
$ vi /etc/sysconfig/docker
```

```java 
# /etc/sysconfig/docker
#
# Other arguments to pass to the docker daemon process
# These will be parsed by the sysv initscript and appended
# to the arguments list passed to docker -d

other_args="--default-ulimit nofile=65535:65535 --insecure-registry 10.1.65.66:8080 --insecure-registry 10.10.15.168:5000 --insecure-registry 10.1.65.227:5000 --registry-mirror=http://6e512fcf.m.daocloud.io --selinux-enabled -H 0.0.0.0:2375 -H unix:///var/run/docker.sock  --insecure-registry 172.18.11.33:5000 --insecure-registry 10.1.64.83:5000  --insecure-registry 10.1.65.66:5000 --insecure-registry 10.1.64.84:5000"
DOCKER_CERT_PATH=/etc/docker
```



### 4.3 重启Docker
修改完以上之后，重启Docker服务。
```
$ sudo service docker restart
```
重启完之后我们再次运行推送命令，把本地镜像推送到私有服务器上。
```
$ sudo docker push 10.1.65.227:5000/base/busybox
```
机可以看到镜像已经push到私有仓库中去了。

接下来我们删除本地镜像，然后从私有仓库中pull下来该镜像。
```
$ sudo docker pull 10.1.65.227:5000/base/busybox
```
### 4.4 私有镜像库简单命令操作

到此就搭建好了Docker私有仓库。上面搭建的仓库是不需要认证的，我们可以结合nginx和https实现认证和加密功能。
管理仓库中的镜像

* 查询：

如果我们想要查询私有仓库中的所有镜像，使用docker search命令：
```
$ docker search registry_ip:5000/
```

* 如果要查询仓库中指定账户下的镜像，则使用如下命令：

```
$ docker search registry_ip:5000/account/
```
### 4.5 私有镜像库V1 HTTP API
* 查询镜像：
```
$ curl -XGET http://10.1.65.227:5000/v1/search
```
* 获取镜像Tag：
```
$ curl -XGET http://10.1.65.227:5000/v1/repositories/base/busybox/tags
```
* 删除镜像指定Tag
```
$ curl -XDELETE http://10.1.65.55:5000/v1/repositories/base/busybox?tag=latest
```
* 删除镜像所有Tag
```
$ curl -XDELETE http://10.1.65.55:5000/v1/repositories/base/busybox/tags
```

### 4.6 私有镜像库V2 HTTP API
* 查看镜像
```
$ curl -XGET http://registry:5000/v2/_catalog
```
* 查看指定镜像的Tag列表
```
$ curl -XGET http://registry:5000/v2/base/busybox/tags/list
```
其他命令详见```https://docs.docker.com/registry/spec/api/```


## 5 搭建Docker Consul集群及相关组件
Consul是一个支持多数据中心分布式高可用的服务发现和配置共享的服务软件，由 HashiCorp 公司用Go语言开发，基于 Mozilla Public License 2.0 的协议开源。本文介绍了如何使用Consul将多个Docker容器组合起来，以提供一个高可扩展的Web服务。

### 5.1 单机集群部署
* 分别启动三个server节点
用bootstrap-expect 3 来启动三个服务节点，并且绑定到容器的同一个ip

node1:
``$ docker run -d --name node1 -h node1 progrium/consul -server -bootstrap-expect 3``

设置网络环境变量:
``$ JOIN_IP="$(docker inspect -f '{{ .NetworkSettings.IPAddress }}' node1)”``

node2:
``docker@boot2docker:~$ docker run -d --name node2 -h node2 progrium/consul -server -join $JOIN_IP``

node3:
``docker@boot2docker:~$ docker run -d --name node3 -h node3 progrium/consul -server -join $JOIN_IP``

* 启动client节点
``$ docker run -d -p 8400:8400 -p 8500:8500 -p 8600:53/udp -h node4 progrium/consul -join $JOIN_IP``

### 5.2 多宿主机集群部署
如果你有多台宿主机部署Consul集群，我建议你采用Swarm+Docker Compose方式部署，简单易上手的网络工具组件Swarm和编排工具Docker Compose 可以大大的降低Docker部署的成本。

* 第一步，准备四台机器
```
10.1.64.158 master
10.1.64.159 node1
10.1.64.83 node2
10.1.64.84 node3
```

* 第二步，安装consul-cluster
master:
```
docker run -d -h master -v /mnt:/data \
-p 8300:8300 \
-p 8301 \
-p 8301:8301/udp \
-p 8302:8302 \
-p 8302:8302/udp \
-p 8400:8400 \
-p 8500:8500 \
progrium/consul -server -advertise 10.1.64.158 -bootstrap-expect 3
```

node1:
```
docker run -d -h node1 -v /mnt:/data \
-p 8300:8300 \
-p 8301:8301 \
-p 8301:8301/udp \
-p 8302:8302 \
-p 8302:8302/udp \
-p 8400:8400 \
-p 8500:8500 \
progrium/consul -server -advertise 10.1.64.159 -join 10.1.64.158
```

node2:
```
docker run -d -h node2 -v /mnt:/data \
-p 8300:8300 \
-p 8301:8301 \
-p 8301:8301/udp \
-p 8302:8302 \
-p 8302:8302/udp \
-p 8400:8400 \
-p 8500:8500 \
progrium/consul -server -advertise 10.1.64.183 -join 10.1.64.158
```

* 第三步，安装consul-client
node3:
```
docker run -d -h node3 -v /mnt:/data \
-p 8300:8300 \
-p 8301:8301 \
-p 8301:8301/udp \
-p 8302:8302 \
-p 8302:8302/udp \
-p 8400:8400 \
-p 8500:8500 \
progrium/consul -advertise 10.1.64.84 -join 10.1.64.158
```

* 此处，小伙伴们要注意了，要将cluster和client的概念分开，不然容易产生混淆，会在cluster节点stop、 exist、dead等情况出现consul集群无法使用的问题。

### 5.3 Registrator
在Docker容器启动后，Registrator配置好相应的环境变量并将这个容器注册到Consul上，示例如下：

``$ docker run -it -v /var/run/docker.sock:/tmp/docker.sock -h $DOCKER_IP progrium/registrator consul://$DOCKER_IP:8500``

Registrator启动后，我们运行一个服务： 
``$ docker run -it -p 8000:80 busybox``

命令执行后，这个服务被自动添加到Consul，同理，如果我们关闭这个服务，它在Consul上会被自动移除。由于Registrator不需要我们手动将服务注册到Consul。
注册成功之后小伙伴们可以通过Consul API进行查询。

* 如果此处小伙伴们启动的是多宿主机的Consul集群，我建议将registrator分别注册到不同的cluster节点上，便于以后日志的统计分析和服务问题快速定位 。

### 5.4 Consul-template
Consul Template的作用是，当它发现Consul上的服务有变化时，它会利用Consul更新文件并执行相应的命令。 
比如说，它能够重写nginx.conf这个文件，将所有服务的路由信息列入其中，然后重新加载Nginx的配置，使得多个相似服务达到负载均衡，或者给多种服务提供一个单一的终端。 

好吧，我们先尝试一下手动build一个consul-template镜像：
* 下载安装consul-template
``$ wget https://releases.hashicorp.com/consul-template/0.7.0/consul-template_0.7.0_linux_amd64.zip``
``unzip consul-template_0.7.0_linux_amd64.zip -d /usr/bin/``

* 查看nginx的dockerfile及相关文件。
```
[root@master nginx]# pwd
/root/nginx
[root@master nginx]# ls
consul-template Dockerfile start.sh
[root@master nginx]# cat Dockerfile 
FROM nginx:latest

ENTRYPOINT ["/bin/start.sh"]
EXPOSE 80
VOLUME /templates
ENV CONSUL_URL consul:8500

ADD start.sh /bin/start.sh
RUN rm -v /etc/nginx/conf.d/*
ADD consul-template /usr/local/bin/
#RUN chmod +x /usr/local/bin/consul-template && chmod +x /bin/start.sh

[root@master nginx]# cat start.sh 
#!/bin/bash
service nginx start
consul-template -consul=$CONSUL_URL -template="/templates/service.ctmpl:/etc/nginx/conf.d/service.conf:service nginx reload"
```

* 定义模版文件与结果文件
```
[root@master ~]# cat /tmp/service.ctmpl 
upstream python-service {
least_conn;
{{range service "python-micro-service"}}server {{.Address}}:{{.Port}} max_fails=3 fail_timeout=60 weight=1;
{{else}}server 127.0.0.1:65535; # force a 502 {{end}}
}

server {
listen 80 default_server;
charset utf-8;

location / {
proxy_pass http://python-service;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
}
}
```


* 构建nginx镜像并并启动。
``$ docker build -t nginx .``
``$ docker run -p 8080:80 -d --name nginx --volume /tmp/service.ctmpl:/templates/service.ctmpl --link consul:consul nginx``

* 查看模版结果。
进入nginx容器，查看配置文件内容。

到此处我们了解了，Docker-Tomplate镜像打包build的过程，唯一遗憾的是我们在启动过程中还需要``--link``来解决数据注册的问题，下面我们来解决一下吧。
我们选择了docker官方主流镜像的Dockerfile来看一下：
* Dockerfile 2
```
FROM nginx:1.7

#Install Curl
RUN apt-get update -qq && apt-get -y install curl

#Download and Install Consul Template
ENV CT_URL http://bit.ly/15uhv24
RUN curl -L $CT_URL | \
tar -C /usr/local/bin --strip-components 1 -zxf -

#Setup Consul Template Files
RUN mkdir /etc/consul-templates
ENV CT_FILE /etc/consul-templates/nginx.conf

#Setup Nginx File
ENV NX_FILE /etc/nginx/conf.d/app.conf

#Default Variables
ENV CONSUL consul:8500
ENV SERVICE consul-8500

# Command will
# 1. Write Consul Template File
# 2. Start Nginx
# 3. Start Consul Template

CMD echo "upstream app {                 \n\
  least_conn;                            \n\
  {{range service \"$SERVICE\"}}         \n\
  server  {{.Address}}:{{.Port}};        \n\
  {{else}}server 127.0.0.1:65535;{{end}} \n\
}                                        \n\
server {                                 \n\
  listen 80 default_server;              \n\
  location / {                           \n\
    proxy_pass http://app;               \n\
  }                                      \n\
}" > $CT_FILE; \
/usr/sbin/nginx -c /etc/nginx/nginx.conf \
& CONSUL_TEMPLATE_LOG=debug consul-template \
  -consul=$CONSUL \
  -template "$CT_FILE:$NX_FILE:/usr/sbin/nginx -s reload";
```

* Dockerfile 1
```
FROM nginx:1.9

#Install Curl
RUN apt-get update -qq && apt-get -y install curl  zip

#Install Consul Template
#RUN curl -L https://github.com/hashicorp/consul-template/releases/download/v0.10.0/consul-template_0.10.0_linux_amd64.tar.gz | tar -C /usr/local/bin --strip-components 1 -zxf -
RUN curl -L https://releases.hashicorp.com/consul-template/0.10.0/consul-template_0.10.0_linux_amd64.zip  
RUN  unzip -o -d /usr/local/bin consul-template_0.10.0_linux_amd64.zip

#Setup Consul Template Files
RUN mkdir /etc/consul-templates
COPY ./app.conf.tmpl /etc/consul-templates/app.conf

# Remove all other conf files from nginx
RUN rm /etc/nginx/conf.d/*

#Default Variables
ENV CONSUL consul:8500

CMD /usr/sbin/nginx -c /etc/nginx/nginx.conf && consul-template -consul=$CONSUL -template "/etc/consul-templates/app.conf:/etc/nginx/conf.d/app.conf:/usr/sbin/nginx -s reload"
```

对比过后发现两个Dockerfile大同小异，但第一个能在启动时打印更多的数据，所以我推崇第一个Dockerfile 的写法。

``docker build -t consul-template``

```
docker run -it \
-e "CONSUL=$DOCKER_IP:8500" \
-e "SERVICE=simple" \
-p 80:80 consul-template
```
上面我们启动的这个consul-template容器将会对在Consul上注册的ServiceName为simple的容器or容器集群进行负载均衡，当服务更新时，consul-template也会通过获取Consul服务的注册信息，及时更新配置文件并平滑重启。


### 5.5 Consul HTTP API

* 查看注册服务信息
``curl -XGET 10.1.64.83:8500/v1/catalog/services``

* 查看单一注册服务详细信息
``curl -XGET 10.1.64.83:8500/v1/catalog/service/[ServiceName]``
例如：

``10.1.64.83:8500/v1/catalog/service/huidao_manage``

```
[
  {
    "Node": "node-server-1-1",
    "Address": "10.1.64.158",
    "ServiceID": "10.1.64.158:huidao_manage:3000",
    "ServiceName": "huidao_manage",
    "ServiceTags": null,
    "ServiceAddress": "10.1.64.158",
    "ServicePort": 10005
  }
]
```

* 删除服务的注册信息
``curl -XDELETE 10.1.64.83:8500/v1/agent/service/deregister/[ServiceID]``

* 特别注意上面是ServiceID

以上为常用API，如需更多详细API使用信息及方法，请查看Consul文档主页[https://www.consul.io/docs/agent/http.html]。















## 6 搭建Docker NSQ集群及相关组件
### 6.1 启动镜像顺序&名令

* nsqlookupd
```
docker run --name lookupd -p 4160:4160 -p 4161:4161 nsqio/nsq /nsqlookupd
```
* nsqd
```
docker run --name nsqd -p 4150:4150 -p 4151:4151 \
    nsqio/nsq /nsqd \
    --broadcast-address=10.1.65.66  \
    --lookupd-tcp-address=10.1.65.66:4160
```
* nsqadmin
```
docker run -d --name nsqadmin -p 4171:4171 nsqio/nsq /nsqadmin  --lookupd-http-address=10.1.65.66:4161
```


### 6.2 NSQ介绍
NSQ 是实时的分布式消息处理平台，其设计的目的是用来大规模地处理每天数以十亿计级别的消息。
NSQ 具有分布式和去中心化拓扑结构，该结构具有无单点故障、故障容错、高可用性以及能够保证消息的可靠传递的特征。 参见 features & guarantees.
NSQ 非常容易配置和部署，且具有最大的灵活性，支持众多消息协议。另外，官方还提供了拆箱即用 Go 和 Python 库。如果读者有兴趣构建自己的客户端的话，还可以参考官方提供的协议规范。

NSQ 是分布式实时消息系统。

NSQ特性:
* 支持无 SPOF 的分布式拓扑
* 水平扩展(没有中间件，无缝地添加更多的节点到集群)
* 低延迟消息传递 (性能)
* 结合负载均衡和多播消息路由风格
* 擅长面向流媒体(高通量)和工作(低吞吐量)工作负载
* 主要是内存中(除了高水位线消息透明地保存在磁盘上)
* 运行时发现消费者找到生产者服务(nsqlookupd)
* 传输层安全性 (TLS)
* 数据格式不可知
* 一些依赖项(容易部署)和健全的，有界，默认配置
* 任何语言都有简单 TCP 协议支持客户端库
* HTTP 接口统计、管理行为和生产者(不需要客户端库发布)
* 为实时检测集成了 statsd
* 健壮的集群管理界面 (nsqadmin)

NSQ担保
对于任何分布式系统来说，都是通过智能权衡来实现目标。通过这些透明的可靠性指标，我们希望能使得 NSQ 在部署到产品上的行为是可达预期的。

* 消息不可持久化（默认）
虽然系统支持消息持久化存储在磁盘中（通过 --mem-queue-size ），不过默认情况下消息都在内存中.

如果将 --mem-queue-size 设置为 0，所有的消息将会存储到磁盘。我们不用担心消息会丢失，nsq 内部机制保证在程序关闭时将队列中的数据持久化到硬盘，重启后就会恢复。

NSQ 没有内置的复制机制，却有各种各样的方法管理这种权衡，比如部署拓扑结构和技术，在容错的时候从属并持久化内容到磁盘。

* 消息最少会被投递一次
如上所述，这个假设成立于 nsqd 节点没有错误。

因为各种原因，消息可以被投递多次（客户端超时，连接失效，重新排队，等等）。由客户端负责操作。

* 接收到的消息是无序的
不要依赖于投递给消费者的消息的顺序。

和投递消息机制类似，它是由重新队列(requeues)，内存和磁盘存储的混合导致的，实际上，节点间不会共享任何信息。

它是相对的简单完成疏松队列，（例如，对于某个消费者来说，消息是有次序的，但是不能给你作为一个整体跨集群），通过使用时间窗来接收消息，并在处理前排序（虽然为了维持这个变量，必须抛弃时间窗外的消息）。

* 消费者最终找出所有话题的生产者
这个服务(nsqlookupd) 被设计成最终一致性。nsqlookupd 节点不会维持状态，也不会回答查询。

网络分区并不会影响可用性，分区的双方仍然能回答查询。部署性拓扑可以显著的减轻这类问题。


### 6.3 NSQ Cluster

下面的步骤将通过推送(publishing)、消费(consuming)和归档(archiving)消息到本地磁盘，在本地环境演示一个小型的 NSQ 集群

* 1、根据文档安装安装 NSQ。

* 2、在另外一个 shell 中，运行 nsqlookupd:

  ```$ nsqlookupd```
再开启一个 shell，运行 nsqd:

  ```$ nsqd --lookupd-tcp-address=127.0.0.1:4160```
再开启第三个 shell，运行 nsqadmin:

  ```$ nsqadmin --lookupd-http-address=127.0.0.1:4161```
开启第四个 shell，推送一条初始化数据(并且在集群中创建一个 topic):

  ```$ curl -d 'hello world 1' 'http://127.0.0.1:4151/put?topic=test'```
最后，开启第五个 shell， 运行 nsq_to_file:

  ```$ nsq_to_file --topic=test --output-dir=/tmp --lookupd-http-address=127.0.0.1:4161```
推送更多地数据到 nsqd:

  ``` $ curl -d 'hello world 2' 'http://127.0.0.1:4151/put?topic=test'```
  ``` $ curl -d 'hello world 3' 'http://127.0.0.1:4151/put?topic=test'```
按照预先设想的，在浏览器中打开 http://127.0.0.1:4171/ 就能查看 nsqadmin 的 UI 界面和队列统计数据。同时，还可以在 /tmp 目录下检查 (test.*.log) 文件.
这个教程中最重要的是：nsq_to_file (客户端)没有明确地指出 test 主题从哪里产生，它从 nsqlookupd 获取信息，即使在消息推送之后才开始连接 nsqd，消息也并没有消失。
* nsqd
nsqd 是一个守护进程，负责接收，排队，投递消息给客户端。

它可以独立运行，不过通常它是由 nsqlookupd 实例所在集群配置的（它在这能声明 topics 和 channels，以便大家能找到）。

它在 2 个 TCP 端口监听，一个给客户端，另一个是 HTTP API。同时，它也能在第三个端口监听 HTTPS。

* 命令行选项:
```
-auth-http-address=: <addr>:<port> 查询授权服务器 (可能会给多次)
-broadcast-address="": 通过 lookupd  注册的地址（默认名是 OS）
-config="": 配置文件路径
-data-path="": 缓存消息的磁盘路径
-deflate=true: 运行协商压缩特性（客户端压缩）
-e2e-processing-latency-percentile=: 消息处理时间的百分比（通过逗号可以多次指定，默认为 none）
-e2e-processing-latency-window-time=10m0s: 计算这段时间里，点对点时间延迟（例如，60s 仅计算过去 60 秒）
-http-address="0.0.0.0:4151": 为 HTTP 客户端监听 <addr>:<port>
-https-address="": 为 HTTPS 客户端 监听 <addr>:<port>
-lookupd-tcp-address=: 解析 TCP 地址名字 (可能会给多次)
-max-body-size=5123840: 单个命令体的最大尺寸
-max-bytes-per-file=104857600: 每个磁盘队列文件的字节数
-max-deflate-level=6: 最大的压缩比率等级（> values == > nsqd CPU usage)
-max-heartbeat-interval=1m0s: 在客户端心跳间，最大的客户端配置时间间隔
-max-message-size=1024768: (弃用 --max-msg-size) 单个消息体的最大字节数
-max-msg-size=1024768: 单个消息体的最大字节数
-max-msg-timeout=15m0s: 消息超时的最大时间间隔
-max-output-buffer-size=65536: 最大客户端输出缓存可配置大小(字节）
-max-output-buffer-timeout=1s: 在 flushing  到客户端前，最长的配置时间间隔。
-max-rdy-count=2500: 客户端最大的 RDY 数量
-max-req-timeout=1h0m0s: 消息重新排队的超时时间
-mem-queue-size=10000: 内存里的消息数(per topic/channel)
-msg-timeout="60s": 自动重新队列消息前需要等待的时间
-snappy=true: 打开快速选项 (客户端压缩)
-statsd-address="": 统计进程的 UDP <addr>:<port>
-statsd-interval="60s": 从推送到统计的时间间隔
-statsd-mem-stats=true: 切换发送内存和 GC 统计数据
-statsd-prefix="nsq.%s": 发送给统计keys 的前缀(%s for host replacement)
-sync-every=2500: 磁盘队列 fsync 的消息数
-sync-timeout=2s: 每个磁盘队列 fsync 平均耗时
-tcp-address="0.0.0.0:4150": TCP 客户端 监听的 <addr>:<port>
-tls-cert="": 证书文件路径
-tls-client-auth-policy="": 客户端证书授权策略 ('require' or 'require-verify')
-tls-key="": 私钥路径文件
-tls-required=false: 客户端连接需求 TLS
-tls-root-ca-file="": 私钥证书授权 PEM 路径
-verbose=false: 打开日志
-version=false: 打印版本
-worker-id=0: 进程的唯一码(默认是主机名的哈希值)
```
### 6.4 NSQ HTTP API

* /ping - 活跃度
* /info - 版本
* /stats - 检查综合运行
* /pub - 发布消息到话题（topic)
* /mpub - 发布多个消息到话题（topic)
* /debug/pprof - pprof 调试入口
* /debug/pprof/profile - 生成 pprof CPU 配置文件
* /debug/pprof/goroutine - 生成 pprof 计算配置文件
* /debug/pprof/heap - 生成 pprof 堆配置文件
* /debug/pprof/block - 生成 pprof 块配置文件
* /debug/pprof/threadcreate - 生成 pprof OS 线程配置文件
``v1 命名空间 (as of nsqd v0.2.29+)``:
* /topic/create - 创建一个新的话题（topic)
* /topic/delete - 删除一个话题（topic)
* /topic/empty - 清空话题（topic)
* /topic/pause - 暂停话题（topic)的消息流
* /topic/unpause - 恢复话题（topic)的消息流
* /channel/create - 创建一个新的通道（channel)
* /channel/delete - 删除一个通道（channel)
* /channel/empty - 清空一个通道（channel)
* /channel/pause - 暂停通道（channel)的消息流
* /channel/unpause - 恢复通道（channel)的消息流
``已抛弃的命名空间``:
* /create_topic - 创建一个新的话题（topic)
* /delete_topic - 删除一个话题（topic)
* /empty_topic - 清空话题（topic)
* /pause_topic - 暂停话题（topic)的消息流
* /unpause_topic - 恢复话题（topic)的消息流
* /create_channel - 创建一个新的通道（channel)
* /delete_channel - 删除一个通道（channel)
* /empty_channel - 清空一个通道（channel)
* /pause_channel - 暂停通道（channel)的消息流
* /unpause_channel - 恢复通道（channel)的消息流

NOTE: 这些结束点返回 "wrapped" JSON:

```
{"status_code":200, "status_text":"OK", "data":{...}}
```
发送 Accept: application/vnd.nsq; version=1.0 头将会协商使用未封装的 JSON 响应格式 (as of nsqd v0.2.29+)。

``/pub``

发布一个消息

参数:
```
topic - the topic to publish to

POST body - the raw message bytes
$ curl -d "<message>" http://127.0.0.1:4151/pub?topic=message_topic`
```
* ``/mpub``
一个往返发布多个消息

参数:
```
topic - 发布到的话题（topic)
binary - bool ('true' or 'false') 允许二进制模式

POST body - `\n` 分离原始消息字节
```
注意：默认的 /mpub 希望消息使用 \n 切割，使用 ?binary=true 查询参数来允许二进制模式，希望发送的消息体能成为以下的格式（HTTP 'Content-Length' 头必须是将要发送的消息体的总大小）：
```
[ 4-byte num messages ]
[ 4-byte message #1 size ][ N-byte binary data ]
      ... (repeated <num_messages> times)
$ curl -d "<message>\n<message>\n<message>" http://127.0.0.1:4151/mpub?topic=message_topic`

```
* ``/topic/create``
已经抛弃的别名 ``/create_topic``

创建一个话题（topic)

参数:
```
话题（topic) - 将要创建的话题（topic)
``/topic/delete``
```

已经抛弃的别名 : ``/delete_topic``

删除一个已经存在的话题（topic) (和所有的通道（channel))

参数:
```
topic - 现有的话题（topic) to delete
* ``/channel/create``
```
已抛弃的别名: ``/create_channel``

为现有的话题（topic) 创建一个通道（channel)

参数:

topic - 现有的话题（topic)
```
channel - the channel to create

* ``/channel/delete``
```

已抛弃的别名: ``/delete_channel``

删除现有的话题（topic) 一个的通道（channel)

参数:

``topic - 现有的话题（topic)``
``channel - 待删除的通道（channel)``

`` /topic/empty``

已抛弃的别名: /empty_topic

清空现有话题（topic) 队列中所有的消息（内存和磁盘中）

参数:

``topic - 待清空的话题（topic)``

* ``/channel/empty``

已抛弃的别名: /empty_channel

清空现有通道（channel) 队列中所有的消息（内存和磁盘中）

参数:

``topic - 现有的话题（topic)``
``channel - 待清空的通道（channel)``

* ``/topic/pause``

已抛弃的别名: ``/pause_topic````

暂停已有话题（topic) 的所有通道（channel)的消息（消息将会在话题（topic) 里排队）

参数:

``topic - 现有的话题（topic)``

* ``/topic/unpause``

已抛弃的别名: ``/unpause_topic``

为现有的话题（topic) 的通道（channel) 重启消息流

参数:

``topic - 现有的话题（topic)``
* ``/channel/pause``

已抛弃的别名: /channel_pause

暂停发送已有的通道（channel) 给消费者（消息将会队列）

参数:

``topic - 现有的话题（topic)``
``channel - 已有的通道（channel)将会被暂停``

* ``/channel/unpause``

已抛弃的别名: /unpause_channel

重新发送通道（channel) 里的消息给消费者

参数:
``topic - 现有的话题（topic)``
``channel - 将要暂停的通道（channel)``
* ``/stats``

返回内部统计数据

参数

format - (可选) `text` or `json` (默认 = `text`)
/ping

监控结束点，必须返回 OK。如果有问题返回 500。同时，如果写消息到磁盘失败将会返回错误状态。

* ``/info``

返回版本信息

* ``/debug/pprof``

可用的调试节点的页码

* ``/debug/pprof/profile``

开始 30秒的 ``pprof`` CPU 配置，并通过请求返回。

注意，因为它在运行时的性能和时间，这个结束点并没在 ``/debug/pprof`` 页面列表中。

* ``/debug/pprof/goroutine``

为所有运行的 goroutines 返回栈记录。

* ``/debug/pprof/heap``

返回堆和内存配置信息（前面的内容可作为 ``pprof`` 配置信息）

* ``/debug/pprof/block``

返回 goroutine 块配置信息

* ``/debug/pprof/threadcreate``

返回 goroutine 栈记录

Debugging and Profiling

nsqd 提供一套节点的配置信息，直接通过 Go 的 pprof 工具。如果你有 go 工具套装，只要运行：
```
* ``memory profiling``
$ go 工具 pprof http://localhost:4151/debug/pprof/heap

* ``cpu profiling``
$ go 工具 pprof http://localhost:4151/debug/pprof/profile
TLS
```
为了加强安全性，可以通过 ``--tls-cert`` 和 ``--tls-key`` 客户端配置 ``nsqd``，升级他们的链接为 TLS。

另外，你可以要求客户端使用 --tls-required (nsqd v0.2.28+)协商 TLS。

你可以通过``--tls-client-auth-policy`` (``require`` 或 ``require-verify``)配置一个 nsqd 客户端证书:

* ``require`` - 客户端必须提供一个证书，否则将会被拒绝
* ``require-verify`` - 客户端必须提供一个有效的证书，根据 ``--tls-root-ca-file`` 指定的链接或者默认的 CA，否则将会被拒绝。
可以当做客户端授权的表单（``nsqd v0.2.28+``）。

如果你想生成一个 password-less，自签名证书，用：

```
$ openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
```
### 6.5 AUTH

* 注意: 在 ``nsqd v0.2.29+`` 可用

通过使用一个遵从 Auth HTTP 协议的授权服务器，指定 ``-auth-http-address=host:port`` 标志，你可以配置 ``nsqd``。

注意: 希望当仅有 ``nsqd`` TCP 协议暴露给外部客户端时使用授权，而不是 HTTP(S) 节点。参见底下说明：

Auth 服务器必须接受 HTTP 请求：

``/auth?remote_ip=...&tls=...&auth_secret=...``
返回结果格式如下：
```
{
  "ttl": 3600,
  "identity": "username",
  "identity_url": "http://....",
  "authorizations": [
    {
      "permissions": [
        "subscribe",
        "publish"
      ],
      "topic": ".*",
      "channels": [
        ".*"
      ]
    }
  ]
}
```
注意话题（topic) 和通道（channel) 字符串必须用 nsqd 的正则表达式来申请授权。nsqd 将会为 TTL 间隔，并会在这个间隔时间里重新请求授权。

通常情况，将会使用 TLS 来加强安全性。``nsqd`` 和 授权服务器间通过信任的网络通信（并没被加密）。如果一个授权服务器通过远程 IP 信息来授权，客户端可以使用占位符（比如 .），作为 AUTH 命令（Auth 服务器忽略）。

授权服务器例子 pynsqauthd。

帮助服务器暴露 ``nsqlookupd`` 和 ``nsqd`` ``/stats`` 数据给客户端，从授权服务器通过权限过滤，在以下可以找到 nsqauthfilter。

当使用命令行工具，可以通过使用 --reader-opt 标志来授权。
```
$ nsq_tail ... -reader-opt="tls_v1,true" -reader-opt="auth_secret,$SECRET"
```

### 6.6 点对点处理延迟

你可以选择设置 ``nsqd`` 来收集和发射点对点信息处理延迟，通过 ``--e2e-processing-latency-percentile`` 标志位来配置百分比。

使用概率百分比技术（参见 Effective Computation of Biased Quantiles over Data Streams）来计算值。我们通过 bmizerany 来使用 perks 包，它能实现这个算法。

我们内部维持 2 个通道（channel)，每个通道（channel)存储 ``N/2`` 分钟的延迟数据。每个 ``N/2`` 分钟我们重置了每个通道（channel)（并开始插入新的数据）。

因为我们仅在通道级别收集数据，对于话题我们聚合并合并所有的通道数量的 quantiles。如果数据在同一个 ``nsqd`` 实例上时，可以使用这个技术。然而当数据已经精确的通过 nsqd (通过 ``nsqlookupd``），我们为每个 ``nsqd`` 取平均值。为了维持统计的精确性，除了平均值，我们也提供最大最小值。

注意: 如果没有消费者连接，不能更新值，尽管消息队列的点对点时间会缓慢增长。这是因为仅在 ``nsqd`` 收到从客户端发来 ``FIN`` 消息时才会重新计算。当消费者重新连接，这些值将会重新调整。

### 6.7 Statsd / Graphite Integration

当使用 ``--statsd-address`` 来为``statsd`` （或类似 ``statsdaemon``）指定 UDP ``<addr>:<port>`` 时，nsqd 将会在 ``--statsd-interval`` 定期推送数据给 statsd（注意：这个间隔必须始终小于等于 graphite 的刷入间隔）。设置 ``nsqadmin`` 可以显示图标。

推荐以下配置（但是这些选择必须建立在你的可用资源和要求上）。同时，``statsd`` 的刷入间隔必须小于或者等于 ``storage-schemas.conf`` 的最小值，并且 ``nsqd`` 必须通过 ``--statsd-interval`` 来确认刷入时间小于等于时间间隔。
```
# storage-schemas.conf
[nsq]
pattern = ^nsq\..*
retentions = 1m:1d,5m:30d,15m:1y

# storage-aggregation.conf
[default_nsq]
pattern = ^nsq\..*
xFilesFactor = 0.2 
aggregationMethod = average
```
``nsqd`` 实例将会推送给以下 ``statsd`` 路径:
```
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.backend_depth [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.depth [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.message_count
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.backend_depth [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.clients [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.deferred_count [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.depth [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.in_flight_count [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.message_count
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.requeue_count
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.timeout_count

# if --statsd-mem-stats is enabled
nsq.<nsqd_host>_<nsqd_port>.mem.heap_objects [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.heap_idle_bytes [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.heap_in_use_bytes [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.heap_released_bytes [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.gc_pause_usec_100 [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.gc_pause_usec_99 [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.gc_pause_usec_95 [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.mem.next_gc_bytes [gauge]
nsq.<nsqd_host>_<nsqd_port>.mem.gc_runs

# if --e2e-processing-latency-percentile is specified, for each percentile
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.e2e_processing_latency_<percent> [gauge]
nsq.<nsqd_host>_<nsqd_port>.topic.<topic_name>.channel.<channel_name>.e2e_processing_latency_<percent> [gauge]
```

```
## 7 Shell脚本部署应用Docker容器
## 8 Jenkin部署应用服务容器
## 9 Nginx 搭建及优化
## 10 Docker Swarm
## 11 Docker Compose