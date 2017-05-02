# MyDockerDoc

#### Docker 服务默认会创建一个 docker0 网桥，它在内核层连通了其他的物理或虚拟网卡，这就将所有容器和本地主机都放到同一个物理网络。
用户也可以指定网桥来连接各个容器，步骤如下：
#### 1. 首先安装bridge-utils工具包
``$ sudo apt-get install bridge-utils``

然后可以用 “brctl show” 来查看当前网桥信息，可以看到目前只有一个 docker0
$ brctl show
``$ bridge name bridge id STP enabled interfaces``
``$ docker0 8000.56847afe9799 no ``
#### 2. 然后创建一个网桥 br0
``$ sudo brctl addbr br0``

``$ sudo ip addr add 192.168.66.1/24 dev br0``

``$ sudo ip link set dev br0 up``

添加后可以用 “brctl show” 来查看

``$ brctl show``
```shell
bridge name bridge id STP enabled interfaces
br0 8000.000000000000 no 
docker0 8000.56847afe9799 no 
```

#### 3. 编辑 /etc/default/docker.io 文件，添加如下Docker参数，是Docker默认使用上面新添加的网桥
``DOCKER_OPTS="-b=br0"``

#### 4. 重启docker服务
``$ sudo service docker.io restart``
#### 5. 新建一个容器，可以看到它已经桥接到了 br0 上了。
#### 6. 最后，如果要删除网桥，可以
``$ sudo ip link set dev br0 down``
``$ sudo brctl addbr br0``
