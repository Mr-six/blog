---
title: 'docker命令详解'
date: 2017-03-29 15:39:53
categories: ['docker']
tags: ['docker']
---
## docker命令详解 ##

###前言
从技术老大那里听说了docker，闲下来之后就简单了解了一下，发现真的是一个好用的不得了的工具。遂周末去图书馆找了下相关资料，学习下姿势。说不定哪天用上了呢😝。在看资料的过程中，发现有不少命令都不知道什么意思，所以干脆做了一个整理，以备以后查阅。ps：在docker命令后输入 `-h` 参数，可查看详细命令。我也是参照某本书上的命令整理的，也不是很全，待以后遇到了继续补充。
> 文中 =[] 表示设置选项时可以设置不同的值，并且可以多次使用 如： -p 8000:80 -p 8080:8080
命令后面的 =false 表示默认值为 false  ="" 表示默认值为空
>若用户不属于docker组，可能需要sudo执行

## docker基础命令

> docker <选项><命令><参数>

`--api-enable-cors=false` 使用API时，启用CORS（cross-origin resource sharing）

`-b 、--bridge=""` 使用事先创建的网桥接口。若设置为none，则不在容器内使用网络

`--bip=""` 使用CIDR标记法设置docker 的IP带宽。该选项不能与`-b`选项同时使用
    如：`-bip="192.168.0.0/24"`
    
`-D 、--debug`  启用调试模式

`-d 、--deamon=false` 以守护进程模式运行

`--dns` 设置docker要使用的DNS服务器

`--dns-search=[]` 设置docker要使用的DNS搜索域。若设置为：example.com，则向服务器查询hello时，将首先查找hello.example.com

`-e、--exec-drive="native"` 设置docker运行驱动，可设置为Native与lxc

`--fixed-cidr=""` 固定分配IPv4地址的带宽。该IP地址必须在`-b`选项设置的网桥网络或`--bip`设置的IP网段内

`-G、--group="docker"` 以守护进程模式运行时，使用`-H`选项创建Unix套接字后，设置该Unix套接字所在的组。使用""空字符串表示不设置分组

`-g、 --graph="/var/lib/docker"` 设置docker使用目录的顶层路径

`-H、--host[]` 以守护进程模式运行时，设置套接字路径。

`--icc=true` 开启容器间通信

`--insecure-registry=[]` 使用私有证书搭建docker注册服务器时，设置docker注册服务器域名

`--ip=0.0.0.0` 使用`docker run` 命令时`-p`选项将端口暴露在外时，设置要绑定的默认IP地址

`--ip-forward=true` 开启net.ipv4.ip_forward

`--ip-masq=true` 为网桥上的IP地址开启IP伪装（masquerading）

`--iptables=true` 开启iptables规则

`--mtu=0` 设置容器的网络最大传输单元（MTU, Maxmum transmission unit）若不设置，则使用路由器的默认MTU或者设置为1500

`-p、--pidfile="/var/run/docker.pid"` 设置PID文件路径

`-registry-mirror=[]` 设置docker registry 的镜像地址

`-s、--storage-driver=[]` 设置储存驱动，默认为auto，也可以设置为drivcemapper

`--selinux-enabled=false`开启SELinux SELinux尚不支持BTRFS储存驱动

`--storage-opt` 设置存储驱动选项

`--tls=false` 使用TLS

`--tlscacert="/home/exapmleuser/.docker/ca.pem"` 设置要在远程证书中使用的CA证书文件的路径

`--tlscert="/home/exapmleuser/.docker/cert.pem"` 设置证书文件路径

`--tlskey="/home/exapmleuser/.docker/key.pem"` 设置密匙文件路径

`tlsverify="false"` 使用TLS远程证书，守护进程与客户端全部使用证书验证

`-v、--version=false` 打印版本信息

## attach命令

> attach命令用于将标准输入（stdin）与标准输出（stdout）连接到正在运行的容器

> docker attach <选项><容器名称, id>

`--no-stdin=false` 不连接标准输入

`--sig-proxy=true` 将所有信号传递给进程（非TTY模式时也一样）但不传送SIGCHLD、SIGKILL、SIGSTOP信号。经常使用的信号如下：
    SIGINT：interrupt信号，输入Ctrl + c 时发生
    SIGQUIT：Quit信号，输入Ctrl + \ 时发生
    EOF：终止attach状态，输入Ctrl + d 时发生
一般会先运行Bash等shell，然后使用docker attach 命令连接到容器，在运行各种命令
`docker run -it --name hello ubuntu /bin/bash`
`docker attach hello`

## build 命令

> build命令使用Dockerfile文件创建镜像

> docker build <选项><Dockerfile路径>

Dockerfile 路径可以使是本地路径，也可以是URL路径。若设置为 - ，则从标准输入获取Dockerfile的内容

`--force-rm=false` 创建镜像失败时，删除临时容器

`--no-cache=false` 不使用之前构建中创建的缓存。

`-q、--quiet=false` 不显示Dockerfile的RUN运行的输出结果

`--rm=true` 创建镜像成功时，删除临时容器

`-t、--tag=""` 设置注册名称、镜像名称、标签。格式为 <注册名称>/<镜像名称>:<标签>（标签默认为latest）

## commit 命令
>commit命令用于从容器的修改项创建显得镜像

> docker commit <选项><容器名称，id>/<镜像名称>:<标签>

`-a、--author=""` 设置奖项创建者的有关信息
`-m、--message=""` 设置有关变更事项的日志信息
`-p、--pause=true` 创建镜像是暂停容器

## cp 命令
> cp命令用于将容器的目录或文件复制的到主机。若将cp命令中的路径设置为目录，则将该目录下的所有内容复制到主机

> docker cp <容器名称>:<路径><主机路径>

`$ docker cp hello:/etc \.` 将容器内的整个/etc 目录复制到主机当前文件夹下

## create 命令
> create 命令使用指定的镜像创建容器。与run命令不同，使用create命令只能创建容器而并不启动

> docker create <选项><镜像名称,id><命令><参数>

`-a 、--attach=[]` 将标准输入、标准输出、标准错误链接到容器
    --attach="stdin"
    
`--add-host=[]` 向容器的/etc/hosts添加主机名与IP地址
    --add-host=hello:192.168.0.233
    
`-C、--cpu-shares=0` 设置cup资源分配。默认是这值为1024，各值为相对值
    若设置为--cpu-shares=2048, 则分配默认值为2倍的CPU资源
    在Linux内核的cgroups中使用该设置的值
`--cap-add[]` 设置容器中使用的cgroups的特定Capablity。若设置为ALL，则使用所有的Capablity
`--cap-drop=[]` 从容器删除cgroup的特定Capablity
`--cidfile=""` 设置cid文件路径。cid中存储着所创建容器的id
`--cpuset=""` 在多核CPU中设置要运行容器的核心数
    若设置--cpuset="0,1" 则使用第一与第二个cup
    若这是--cupset="0-2" 则使用从第一到第三个cup
`--device=[]` 添加主机设备到容器，格式为<主机设备>:<容器设备>
    若设置为 --device="/dev/sda1:/dev/sda1",则在容器中也可以使用主机的/dev/sda1块设备
    
`--dns=[]` 设置容器中要用到的DNS服务器

`--dns-search=[]` 设置docker要使用的DNS搜索域。

`-e、--env=[]` 向容器设置环境变量。一般用于传递设置或者密码
    如：-e MYSQL_ROOT_PASSWORD=root
`--entrypoint=""` 忽略Dockerfile的ENTRYPOINT设置，强制设置为其他值。
    如：--entrypoint="/bin/bash"
`--env-file=[]` 向容器应用设置环境变量文件
`--expose=[]` 仅连接容器的端口与主机，并不暴露在外
    --expose="3306"
`-h、--hostname=""` 设置容器主机名
`-i、--interactive=false` 激活标准输入，即使未与容器连接（attach），也维持标准输入。一般使用该选项向Bash输入命令
`--link=[]` 进行容器连接，格式为<容器名称>:<别名>
    --link mysql-server:mysql

`--lxc-conf=[]` 若使用LXC驱动，则可以设置LXC选项
    --lxc-conf="lxc.cgroup.cpuset.cpu = 0,1"    

`-m、--memory=""` 设置内存限制，格式为<数字><单位>，单位可以使用b,k,m,g
    --memory="512m"  
`--name` 设置容器名称
`--net="bridge"` 设置容器的网络模式（选项可以是：bridge,none,container,host）
`-P、--publish-all=false` 将连接到主机的容器的所有端口暴露在外
`-p、--publish=[]` 将连接到主机的容器的特定端口暴露在外。一般主要用于暴露web服务器的端口
`--privileged=false` 在容器内部使用主机的所有Linux内核功能
`--restart=""` 设置容器内部进程终止时重启策略
    --restart=no 即使进程终止也不重启
    --restart="on-failure" 仅当进程的Exit Code 不为0时执行重启。也可以设置重置次数。若不设置重试次数，这不断重启。如 --restart="no-failure:10"
    --restart="always" 不受Exit Code的影响，总是重启
`--security-opt=[]` 设置SELinux、AppArmor 选项
`-t、--tty=false` 使用TTY模式（pseudo-TTY）。若要使用Bash，则必须设置该选项。若不设置该选项，则可以输入命令，但不显示shell
`-u、--user=""` 设置容器运行时要使用的Linux用户账户与UID
`-v、--volume=[]` 设置数据卷。设置要与主机共享目录，不将文件保存到容器，而直接保存到主机。在主机目录后添加 :ro、:rw进行读写设置，默认为:rw。
`--volumes-from=[]` 连接数据卷容器，设置格式为<容器名，id>:<:ro, :rw> 默认情形下，读写设置遵从-v选项的设置。
`-w、--workdir=""` 设置容器内部要运行进程的目录

    运行如下命令，创建容器
    $ docker create -it --name hello ubuntu /bin/bash
    若想使用刚刚创建的容器，则必须使用docker start 命令启动容器
    $ docker start hello
    进入容器内部
    $ docker attach hello

## diff 命令
> diff命令用于检查容器文件系统的修改

> docker diff <容器名称，id>

比较文件是否修改的标准是容器创建时的镜像内容
    A：添加的文件
    C：修改的文件
    D：删除的文件
## events 命令
> events命令用于实时输出Docker服务器中发生的事件

> docker events

`--since=""` 输出特定的timestamp之后的事件
`--until=""` 输出特定的timestamp之前的事件
运行docker events命令，进入待机状态
    $ docker events
在另一终端，运行容器
    $ docker start hello  #假设容器已存在
就会在刚刚的docker events 命令窗口看到 运行hello 容器的事件
## exec 命令
> exec命令用于从外部运行容器内部的命令

> docker exec <选项><容器名称，id><命令><参数>

`-d、--detach=false` 以后台模式运行命令
`-i、--interactive=false` 开启标准输入，即使未与容器连接，也维持标准输入
`-t、--tty=false` 使用TTY模式（pseudo-TTY）若要使用bash，则必须设置该选项。若不设置该选项，则虽然输入命令，但不显示shell
运行如下命令，创建容器
    $ docker run -d --name hello ubuntu /bin/bash -c "while true; do echo Hello World; sleep 1; done"
设置每隔一秒输出一次hello world。在此状态下，运行容器内部的/bin/bash,连接至bash shell ，如下所示。连接bash shell 时，只有使用 -i -t 选项才能输入命令并查看结果
    $ docker exec -it hello /bin/bash  #连接容器
    $ ps ax  # 查看进程
![图片描述][1]
若在容器内部运行ps ax 命令，则可以看到由docker exec 命令运行的其他/bin/bash，与输出hello world 的/bin/bash 不是同一个。输入exit命令退出Bash shell后，容器不会停止，而会继续运行。像这样，灵活使用 docker exec 命令将Bash shell 连接到正在运行守护进程的容器上，并行多种操作
如下：不连接Bash shell，而使用apt-get等命令，在容器内安装redis-server包，
    $ docker exec hello apt-get update
    $ docker exec hello apt-get install -y redis-server
    $ docker exec -d hello redis-server # 后台运行rides-server
## export 命令
> export命令将用于将容器的文件系统导出为tar文件包

> docker export <容器名称，id>

只运行docker export 命令后，由于容器的内容会输出到标准输出，所以必须设置重定向
    $ docker run -it -d --name hello ubuntu /bin/bash
    $ docker export hello > hello.tar

## history 命令
> history 命令用于显示镜像的历史。此处的历史依据Dockerfile文件中的设置创建。

> docker history <选项><镜像名称，id>

`--no-trunc=false` 输出所有因内容过长而省略的部分
`-q、--quiet=false` 只显示镜像id
## images 命令
> images命令用于输出镜像列表

> docker images <选项><镜像名称，id> 

`-a、--all=false` 列出所有镜像，包括父镜像
`-f、--filter=[]` 设置输出结果过滤。若设置为"dangling=true"，则只输出无名镜像
`--no-trunc=false` 显示所有因内容过长而省略的部分
## import 命令
> import命令用于从压缩为tar文件（.tar \.tar.gz \.tgz \.bzip \.tar.xz \.txz）的文件系统创建镜像

> docker import <tar文件的URL或者 - ><注册名称>/<镜像名称>:<标签>


使用import命令时，可以设置tar文件的URL，若设置为 - ，则从标准输入接收tar文件的内容。既可以使用由docker export 命令创建的tar文件，也可以直接组织文件系统。
    
    $ docker import http://example.com/hello.tar.zg hello
下列命令中使用本地的 hello.tar 文件的内容通过管道传递给 docker import 命令
    $ cat hello.tar | docker import - hello
若想将当前目录的内容直接创建为镜像：
    $ tar -c \. | docker import - hello

## info 命令
> info命令用于显示当前系统信息、docker容器、镜像个数、设置等信息。

> docker info
![图片描述][2]

## inspect 命令
> inspect 命令用于以JSON格式显示容器与镜像的详细信息

> docker inspect <选项><容器或镜像名称，id>

`-f、--format=""` 只显示指定信息。如："{ {.NetworkSettings.IPAddress}}" 使用 \. 来设置JSON文档的下层项目
下面命令显示容器的IP地址
    $ docker run -it -d --name hello ubuntu /bin/bash
    $ docker inspect -f "{ {.NetworkSettings.IPAddress}}" hello
   
下面命令只从容器的详细信息中抽取特定部分，并按照所希望的格式显示    

    $ docker run -it -d --name hello -p 8000:80 -p 8080:8080 ubuntu /bin/bash
    $ docker inspect -f '{ {range $p, $conf := \.NetworkSettings.Ports}} { {$p}} -> { {(index $conf 0).HostPort}} { {end}}' hello
 ![图片描述][3]
 此处使用 { {range $p, $conf := \.NetworkSettings.Ports}} 循环访问 \.NetworkSettings.Ports 的值，并代入 $p $conf。然后输出$p,并将$conf数组的第一项 (index $conf 0) 的 \.HostPort 输出。
 另：.NetworkSettings.Ports 是一个map类型数据结构：
     map[80/tcp:[{0.0.0.0 8000}] 8080/tcp:[{0.0.0.0 8080}]]

## kill 命令
> kill命令用于向容器发送KILL信号，从而关闭容器（推荐使用更优雅温和的 docker stop 命令)

> docker <选项><容器名称，id>

`-s、--signal="KILL"` 发送特定信号

## load 命令

> load命令用于从tar文件创建镜像

> docker load <选项>

将tar文件发送到 docker load 命令的标准输入，然后创建镜像。tar文件由 docker save 命令创建，包含镜像名称与标签。
`-i、--input=""` 不使用标准输入，设置文件路径并创建镜像。

    $ docker save myimages > myimages.tar  #将已存在的镜像保存为tar文件
    $ docker load < myimages.tar  #在另一台电脑从tar文件创建镜像

## login 命令
> login命令用于登录Docker 的注册服务器
> docker login <选项><Docker 注册服务器的URL>

若不设置注册服务器的地址，则默认登录[dockerhub](https://hub.docker.com/)（api  https://index.docker.io/v1/）
`-e、--email=""` 设置登录时使用的电子邮件
`-p、--password=""` 设置登录密码
`-u、--username=""` 设置登录时使用的账号
## logout 命令
> logout命令用于从Docker注册服务器中登出

> docker logout <选项><Docker 注册服务器的URL>

若不设置注册服务器的地址，则默认为[dockerhub](https://hub.docker.com/)（api  https://index.docker.io/v1/）

## logs 命令

> logs命令用于输出容器日志

> docker logs <容器名称，id>

`-f、--follow=false` 一直输出实时日志
`-t、--timestamp=false` 在登录时显示时间值
`--tail="all"` 指定数字，只从日志中输出一定个数

## port 命令

> port命令用于查看容器的某个端口是否处于开放状态

> docker port <容器名称，id><端口>

![图片描述][4]

## pause 命令

> pause命令用于暂停容器中正在运行的所有进程

> docker pause <容器名称，id>

![图片描述][5]

## ps 命令

> ps命令用于输出容器列表

> docker ps <选项>

`-a、--all=false` 列出所有容器。不带 `-a` 只输出在运行的容器
`--before=""` 列出特定容器创建前的容器，包含停止的容器。
`-f、--filter=[]` 设置输出过滤。如 "exited=0"
`-l、--latest=false` 列出最后创建的容器，包含停止的容器
`-q、--quiet=false` 只输出容器的id

## push 命令

> push命令用于将镜像推送到Docker注册服务器

> docker push <注册名>/<镜像名>:<标签>

注册名中既可以设置Docker Hub 的用户名，也可以设置注册地址
若不设置标签，则推送所有标签的镜像
    $ docker pull user/hello:latest
如下推送到个人仓库
    $ docker pull 192.168.0.33:6666/hello:latest
    $ docker pull yourset.com:6666/hello:latest

## restart 命令
> restart命令用户重启容器

> docker restart <选项><容器名称，id>

`-t、time=10` 设置从容器停止到重启的等待时间，单位为秒
    $ docker restart hello

## rm 命令
> rm 命令用于删除容器

> docker rm <选项><容器名称，id>

`-f、--force=false` 强制停止容器后删除（使用SIGKILL信号）
`-l、--link=false` 在docker run 命令中使用--link 选项，只删除连接，不删除容器。
`-v、--volumes=false` 删除连接到容器的数据卷
若要一次删除所有容器，可在docker ps：命令中使用 `-a -q` 选项获取容器id只有传给docker rm 命令
    $ docker rm `docker ps -aq`
    $ docker rm $(docker ps -aq)
    
## rmi 命令

> rmi命令用于删除镜像。若不指定标签，则删除latest标签

> docker rim <注册名称>/<镜像名称，id>:<标签>

`-f、--force=false` 强制删除镜像
`--no-prune=false` 不删除不带标签的父级镜像
    $ docker rmi hello
    $ docker rmi user/hello:latest
    $ docker rmi 192.168.0.33:6666/hello:latest  #远程仓库镜像
    $ docker pull yourset.com:6666/hello:latest  #远程仓库镜像
删除所有镜像与删除容器类似
    $ docker rmi `docker images -aq`

## run 命令
> run命令用于指定镜像创建容器

> docker run <选项><镜像名称，id><命令><参数>

docker run 命令 与 docker create 基本类似 唯一的不同是 run命令在创建容器后会启动容器，所以参数基本类似，只是多了关于启动后的设置，一下是多出来的命令：

`-d、--detach` Detach模式，一般为守护进程模式，容器以后台方式运行
`--rm=false` 若容器内的进程终止，则自动删除容器，此选项不能与`-d`选项一起使用
`--sig-proxy=true` 将所有信号传递给进程（非TTY模式时也一样），但不传递SIGCHLD、SIGKILL、SIGSTOP信号

## save 命令
> save命令用于将镜像保存为tar包文件

> docker save <选项><镜像名称>:<标签>

`-o、--output=""` 设置保存的文件名
若不设置`-o`选项，tar文件会输出到标准输出，所以必须设置重定向。如果仅指定镜像名称而未指定标签，则将所有标签保存到一个tar文件。
    
## search 命令
> search命令用与在docker hub 中搜索镜像

> docker search <选项><搜索词>

`--automated=false` 只显示由docker hub 的automated build 创建的镜像
`--no-trunc=false` 显示所有因因为内容过长而省略的部分
`-s、--stars=0` 显示滴啊有特定星级以上的镜像
 
## start 命令
> start命令用于启动容器

> docker start <选项><容器名称，id>

`-a、--attrch=false` 将标准输入、标准输出、标准错误连接到容器，传递所有信号
`-i、--interactive=false` 激活标准输入
    
## stop 命令
> stop命令用于终止容器

> docker stop <选项><容器名称，id>

`-t、--time=10` 设置终止容器前的等待时间，单位为秒
    
## tag 命令
> tag命令用于设置镜标签

> docker tag <选项><镜像名称>:<标签><注册地址，用户名>/<镜像名称>:<标签>

`-f、--force=false` 即使已拥有标签也强制设置
如将远程仓库设置标签
    $ docker tag hello:latest user/hello:0.1  #设置docker hub上的
    $ docker tag hello:latest youset:6666/hello:0.1  #私人仓库
    
## top 命令
> top命令用于显示容器中正在运行的进程信息

> docker top <容器名称，id><ps选项>

在<ps选项>中设置 Linux ps 命令的选项 [参考](http://man.linuxde.net/ps)
    $ docker top hello aux
    
## unpause 命令
> unpause命令用于重启 pause 命令暂停的容器

> docker unpause <容器名称，id>
    
## version 命令
    > version命令用于输出docker的版本信息
    
    > docker version
 
## wait 命令

> wait 命令等待容器终止，然后输出 Exit Code

> docker wait <容器名称，id>

## 后记
单一的容器一般不能满足业务需要，需要一个编排的工具。Docker Compose和Docker Swarm 正是负责快速在集群中部署分布式应用。漫漫长路，学的还有好多，工作虽不是负责这方面的，我想做的只是将自己的想法运行在代码是而已。



  [1]: https://segmentfault.com/img/bVK9Cx?w=1488&h=272
  [2]: https://segmentfault.com/img/bVLanU?w=1484&h=822
  [3]: https://segmentfault.com/img/bVLchf?w=2366&h=234
  [4]: https://segmentfault.com/img/bVLclB?w=864&h=148
  [5]: https://segmentfault.com/img/bVLcl6?w=2250&h=322
