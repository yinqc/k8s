IP Address

IP地址192.168.1.1子网掩码255.255.255.0起始地址和结束地址是多少？

这是标准的私网地址，一共有256个地址，起始地址是192.168.1.0到192.168.1.255，其中首末两地址不可分配给计算机等设备，因其为特殊地址，192.168.1.0为域地址，就是网络地址，而192.168.1.255为广播地址。故实际可用的地址是从192.168.1.1到192.168.1.254，共254个。



DHCP



## 动手实践才是最好的学习方式



对于 VirtualBox，首先，你需要在“工具 - 网络”里创建一个“Host-only”的网络，IP 地址段随意，比如这里就使用了它自动分配的“192.168.56.1/24”。然后，在虚拟机的配置里，你需要启用两个网卡。“网卡 1”就设置成刚才创建的“Host-only”网络，它是我们在本地终端登录和联网时用的；而“网卡 2”是“网络地址转换（NAT）”，用来上外网。

sudo apt update
sudo apt install -y git vim curl jq openssh-server
ip addr



在VS Code上安装GO插件

https://zhuanlan.zhihu.com/p/517836734

```text
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn
```

上述配置都完成后，再次按住"ctrl+shift+p"打开extention，找到“Go: Install/Update Tools”

重启VS Code。

[http://go-edu.cn/categories/Go-语言基础教程/](http://go-edu.cn/categories/Go-语言基础教程/)



从github上导入k8s代码



## 01 初识容器

apt show pakage

sudo apt install -y docker.io #安装Docker Engine

sudo service docker start         #启动docker服务
sudo usermod -aG docker ${USER}   #当前用户加入docker组



第一个 service docker start 是启动 Docker 的后台服务，第二个 usermod -aG 是把当前的用户加入 Docker 的用户组。这是因为操作 Docker 必须要有 root 权限，而直接使用 root 用户不够安全，加入 Docker 用户组是一个比较好的选择，这也是 Docker 官方推荐的做法。当然，如果只是为了图省事，你也可以直接切换到 root 用户来操作 Docker。上面的三条命令执行完之后，我们还需要退出系统（命令 exit ），再重新登录一次，这样才能让修改用户组的命令 usermod 生效。



docker pull busybox      #拉取busybox镜像

我们再执行命令 docker images ，它会列出当前 Docker 所存储的所有镜像

docker run busybox echo hello world

然后我们再用 docker ps 命令，加上一个参数 -a ，就可以看到这个已经运行完毕的容器



下面的这张图来自 Docker 官网（https://docs.docker.com/get-started/overview/），精准地描述了 Docker Engine 的内部角色和工作流程，对我们的学习研究非常有指导意义。



## 02 被隔离的进程：一起来看看容器的本质

广义上来说，容器技术是动态的容器、静态的镜像和远端的仓库这三者的组合。



docker pull alpine

docker run -it alpine sh

注意我们在这里多加了一个 -it 参数，这样我们就会暂时离开当前的 Ubuntu 操作系统，进入容器内部。

容器，就是一个特殊的隔离环境，它能够让进程只看到这个环境里的有限信息，不能对外界环境施加影响。

![img](https://static001.geekbang.org/resource/image/26/6d/26cb446ac5ec53abde2744c431200c6d.jpg?wh=1920x869)

其实奥秘就在于 Linux 操作系统内核之中，为资源隔离提供了三种技术：namespace、cgroup、chroot，虽然这三种技术的初衷并不是为了实现容器，但它们三个结合在一起就会发生奇妙的“化学反应”。



## 03 容器化的应用

IMAGE ID 还有一个好处，因为它是十六进制形式且唯一，Docker 特意为它提供了“短路”操作，在本地使用镜像的时候，我们不用像名字那样要完全写出来这一长串数字，通常只需要写出前三位就能够快速定位，在镜像数量比较少的时候用两位甚至一位数字也许就可以了。

使用名字和 IMAGE ID 来删除镜像：

docker rmi redis    
docker rmi d4c



基本的格式是“docker run 设置参数”，再跟上“镜像名或 ID”，后面可能还会有附加的“运行命令”。

docker run -d nginx:alpine            # 后台运行Nginx
docker run -d --name red_srv redis    # 后台运行Redis
docker run -it --name ubuntu 2e6 sh   # 使用IMAGE ID，登录Ubuntu18.04

docker run **-h srv** alpine hostname # srv 是指定的hostname

**对于正在运行中的容器，我们可以使用 docker exec 命令在里面执行另一个程序**，效果和 docker run 很类似，但因为容器已经存在，所以不会创建新的容器。它最常见的用法是使用 -it 参数打开一个 Shell，从而进入容器内部，例如：

docker exec -it red_srv sh



![img](https://static001.geekbang.org/resource/image/c8/85/c8cd008e91aaff2cd91e0392b0079085.jpg?wh=1920x1747)





## 04 创建容器镜像：如何编写正确、高效的Dockerfile



好了，今天我们一起学习了容器镜像的内部结构，重点理解容器镜像是由多个只读的 Layer 构成的，同一个 Layer 可以被不同的镜像共享，减少了存储和传输的成本。

如何编写 Dockerfile 内容稍微多一点，我再简单做个小结：

创建镜像需要编写 Dockerfile，写清楚创建镜像的步骤，每个指令都会生成一个 Layer。

Dockerfile 里，第一个指令必须是 FROM，用来选择基础镜像，常用的有 Alpine、Ubuntu 等。

其他常用的指令有：COPY、RUN、EXPOSE，分别是拷贝文件，运行 Shell 命令，声明服务端口号。

docker build 需要用 -f 来指定 Dockerfile，如果不指定就使用当前目录下名字是“Dockerfile”的文件。

docker build 需要指定“构建上下文”，其中的文件会打包上传到 Docker daemon，所以尽量不要在“构建上下文”中存放多余的文件。

创建镜像的时候应当尽量使用 -t 参数，为镜像起一个有意义的名字，方便管理。

今天讲了不少，但关于创建镜像还有很多高级技巧等待你去探索，比如使用缓存、多阶段构建等等，你可以再参考 Docker 官方文档（https://docs.docker.com/engine/reference/builder/），或者一些知名应用的镜像（如 Nginx、Redis、Node.js 等）进一步学习。



## 05 镜像仓库

官方镜像是指 Docker 公司官方提供的高质量镜像（https://github.com/docker-library/official-images）

- 镜像仓库（Registry）是一个提供综合镜像服务的网站，最基本的功能是上传和下载。

- Docker Hub 是目前最大的镜像仓库，拥有许多高质量的镜像。上面的镜像非常多，选择的标准有官方认证、下载量、星数等，需要综合评估。

- 镜像也有很多版本，应该根据版本号和操作系统仔细确认合适的标签。

- 在 Docker Hub 注册之后就可以上传自己的镜像，用 docker tag 打上标签再用 docker push 推送。

- 离线环境可以自己搭建私有镜像仓库，或者使用 **docker save** 把镜像存成压缩包，再用 **docker load** 从压缩包恢复成镜像。

```bash
# 拉取镜像
docker pull hyper/docker-registry-web
# 启动容器 --link myRegistry 是我以前安装的 Registry 
cdocker run -d -p 5001:8080 --name registry-web --restart=always --link myRegistry -e registry_url=http://registry:5000/v2 -e registry_name=localhost:5000 hyper/docker-registry-web:latest
```



## 06 容器该如何与外界互联互通

**如何拷贝容器内的数据**

docker cp a.txt 062:/tmp

docker exec -it 062 sh



docker cp 062:/tmp/a.txt ./b.txt



**如何共享主机上的文件**

**只需要在 docker run 命令启动容器的时候使用 -v 参数就行**，具体的格式是“宿主机路径: 容器内路径”。

docker run -d --rm -v /tmp:/tmp redis

**如何实现网络互通**

null 是最简单的模式，也就是没有网络，但允许其他的网络插件来自定义网络连接，这里就不多做介绍了。host 的意思是直接使用宿主机网络，相当于去掉了容器的网络隔离（其他隔离依然保留），所有的容器会共享宿主机的 IP 地址和网卡。这种模式没有中间层，自然通信效率高，但缺少了隔离，运行太多的容器也容易导致端口冲突。host 模式需要在 docker run 时使用 --net=host 参数，下面我就用这个参数启动 Nginx。

**如何分配服务端口号**



## 07 实战演练

目前最知名的公开镜像仓库是 Docker Hub，其他的还有 quay.io、gcr.io，我们可以在这些网站上找到许多高质量镜像，集成到我们自己的应用系统中。

容器技术有很多具体的实现，Docker 是最初也是最流行的容器技术，它的主要形态是运行在 Linux 上的“Docker Engine”。我们日常使用的 docker 命令其实只是一个前端工具，它必须与后台服务“Docker daemon”通信才能实现各种功能。



操作容器的常用命令有 docker ps、docker run、docker exec、docker stop 等；操作镜像的常用命令有 docker images、docker rmi、docker build、docker tag 等；操作镜像仓库的常用命令有 docker pull、docker push 等。

私有镜像仓库有很多现成的解决方案，今天我只选择最简单的 Docker Registry，而功能更完善的 CNCF Harbor 留到后续学习 Kubernetes 时再介绍。你可以在 Docker Hub 网站上搜索“registry”，找到它的官方页面（https://registry.hub.docker.com/_/registry/）：

docker run -d -p 5000:5000 registry

docker tag nginx:alpine 127.0.0.1:5000/nginx:alpine

docker push 127.0.0.1:5000/nginx:alpine

docker rmi  127.0.0.1:5000/nginx:alpine
docker pull 127.0.0.1:5000/nginx:alpine



curl 127.1:5000/v2/_catalog
curl 127.1:5000/v2/nginx/tags/list





UUID=7bd3f845-4249-494a-b8d4-f4732dd31d48 /var   ext4  errors=remount-ro 0    1



![img](https://static001.geekbang.org/resource/image/79/16/79f8c75e018e0a82eff432786110ef16.jpg?wh=1920x2142)





## 09 如何在本机搭建小巧完备的Kubernetes环境

由于 Kubernetes 背后有 Borg 系统十多年生产环境经验的支持，技术底蕴深厚，理论水平也非常高，一经推出就引起了轰动。然后在 2015 年，Google 又联合 Linux 基金会成立了 CNCF（Cloud Native Computing Foundation，云原生基金会），并把 Kubernetes 捐献出来作为种子项目。

简单来说，Kubernetes 就是一个生产级别的容器编排平台和集群管理系统，不仅能够创建、调度容器，还能够监控、管理服务器，它凝聚了 Google 等大公司和开源社区的集体智慧，从而让中小型公司也可以具备轻松运维海量计算节点——也就是“云计算”的能力。



**kind 基于 Docker，意思是“Kubernetes in Docker”。**它功能少，用法简单，也因此运行速度快，容易上手。不过它缺少很多 Kubernetes 的标准功能，例如仪表盘、网络插件，也很难定制化，所以我认为它比较适合有经验的 Kubernetes 用户做快速开发测试，不太适合学习研究。

**再来看 minikube，从名字就能够看出来，它是一个“迷你”版本的 Kubernetes**

https://minikube.sigs.k8s.io/docs/start/



**不过 minikube 只能够搭建 Kubernetes 环境，要操作 Kubernetes，还需要另一个专门的客户端工具“kubectl”。**

minikube kubectl



所以，在 minikube 环境里，我们会用到两个客户端：minikube 管理 Kubernetes 集群环境，kubectl 操作实际的 Kubernetes 功能，和 Docker 比起来有点复杂。



![img](https://static001.geekbang.org/resource/image/22/e3/22c4d6ef48a0cf009946ebbbc31b91e3.jpg?wh=1920x1406)



使用命令 minikube start 会从 Docker Hub 上拉取镜像，以当前最新版本的 Kubernetes 启动集群。不过为了保证实验环境的一致性，我们可以在后面再加上一个参数 --kubernetes-version，明确指定要使用 Kubernetes 版本。

minikube start

minikube status
minikube node list

还可以用命令 minikube ssh 登录到这个节点上，虽然它是虚拟的，但用起来和实机也没什么区别

minikube kubectl -- version 

alias kubectl="minikube kubectl --"



https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

配置镜像加速器

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://mhgl22zn.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

**minikube start --image-mirror-country='cn'**

https://developer.aliyun.com/article/221687 Minikube - Kubernetes本地实验环境



https://github.com/AliyunContainerService/minikube

```
minikube start --image-mirror-country='cn' --cpus=4 --memory=4096mb
```



kubectl run ngx --image=nginx:alpine

kubectl get pod



另外还要说一下 Kubernetes 的官网（https://kubernetes.io/zh/），里面有非常详细的文档，包括概念解释、入门教程、参考手册等等，最难得的是它有全中文版本，我们阅读起来完全不会有语言障碍，希望你有时间多上去看看，及时获取官方第一手知识。

容器编排 Container Orchestration

现在大家谈到容器都会说是 Docker，但其实早在 Docker 之前，Google 在公司内部就使用了类似的技术（**cgroup 就是 Google 开发再提交给 Linux 内核的**），只不过不叫容器。

那么，Kubernetes 到底能够为我们做什么呢？https://kubernetes.io/zh-cn/

简单来说，Kubernetes 就是一个生产级别的容器编排平台和集群管理系统，不仅能够创建、调度容器，还能够监控、管理服务器，它凝聚了 Google 等大公司和开源社区的集体智慧，从而让中小型公司也可以具备轻松运维海量计算节点——也就是“云计算”的能力。 





## 10 Kubernetes 的内部架构和工作机制

从某种角度来看，Kubernetes 可以说是一个集群级别的操作系统，主要功能就是资源管理和作业调度。

Kubernetes 采用了现今流行的“控制面 / 数据面”（Control Plane / Data Plane）架构，集群里的计算机被称为“节点”（Node），可以是实机也可以是虚机，少量的节点用作控制面来执行集群的管理维护工作，其他的大部分节点都被划归数据面，用来跑业务应用。

**Kubernetes 的基本架构**

操作系统的一个重要功能就是抽象，从繁琐的底层事务中抽象出一些简洁的概念，然后基于这些概念去管理系统资源。



Master里的组件有哪些

kubectl get pod -n kube-system

![img](https://static001.geekbang.org/resource/image/33/c6/330e03a66f636657c0d8695397c508c6.jpg?wh=1278x704)



Node里的组件有哪些

Master 里的 apiserver、scheduler 等组件需要获取节点的各种信息才能够作出管理决策，那这些信息该怎么来呢？这就需要 Node 里的 3 个组件了，分别是 kubelet、kube-proxy、container-runtime。

![img](https://static001.geekbang.org/resource/image/87/35/87bab507ce8381325e85570f3bc1d935.jpg?wh=1278x704)



使用 minikube ssh 命令登录到节点后，可以用 docker ps 看到 kube-proxy：

minikube ssh
docker ps |grep kube-proxy



而 kubelet 用 docker ps 是找不到的，需要用操作系统的 ps 命令：

ps -ef|grep kubelet



现在，我们再把 Node 里的组件和 Master 里的组件放在一起来看，就能够明白 Kubernetes 的大致工作流程了：

每个 Node 上的 kubelet 会定期向 apiserver 上报节点状态，apiserver 再存到 etcd 里。

每个 Node 上的 kube-proxy 实现了 TCP/UDP 反向代理，让容器对外提供稳定的服务。

scheduler 通过 apiserver 得到当前的节点状态，调度 Pod，然后 apiserver 下发命令给某个 Node 的 kubelet，kubelet 调用 container-runtime 启动容器。

controller-manager 也通过 apiserver 得到实时的节点状态，监控可能的异常情况，再使用相应的手段去调节恢复。



![img](https://static001.geekbang.org/resource/image/65/e1/65d38ac50b4f2f1fd4b6700d5b8e7be1.jpg?wh=1920x1096)





## 11 YAML: Kubernetes世界里的通用语



什么是API对象

作为一个集群操作系统，Kubernetes 归纳总结了 Google 多年的经验，在理论层面抽象出了很多个概念，用来描述系统的管理运维工作，这些概念就叫做“API 对象”。说到这个名字，你也许会联想到上次课里讲到的 Kubernetes 组件 apiserver。没错，它正是来源于此。



因为 apiserver 是 Kubernetes 系统的唯一入口，外部用户和内部组件都必须和它通信，而它采用了 HTTP 协议的 URL 资源理念，API 风格也用 RESTful 的 GET/POST/DELETE 等等，所以，这些概念很自然地就被称为是“API 对象”了。



kubectl api-resources



kubectl get pod --v=9



如何编写YMAL

第一个技巧其实前面已经说过了，就是 kubectl api-resources 命令，它会显示出资源对象相应的 API 版本和类型，比如 Pod 的版本是“v1”，Ingress 的版本是“networking.k8s.io/v1”，照着它写绝对不会错。



第二个技巧，是命令 kubectl explain，它相当于是 Kubernetes 自带的 API 文档，会给出对象字段的详细说明，这样我们就不必去网上查找了。比如想要看 Pod 里的字段该怎么写，就可以这样：

kubectl explain pod
kubectl explain pod.metadata
kubectl explain pod.spec
kubectl explain pod.spec.containers



这第三个技巧就是 kubectl 的两个特殊参数 --dry-run=client 和 -o yaml，前者是空运行，后者是生成 YAML 格式，结合起来使用就会让 kubectl 不会有实际的创建动作，而只生成 YAML 文件。

kubectl run ngx --image=nginx:alpine --dry-run=client -o yaml



这个小技巧还可以再进化一下，把这段参数定义成 Shell 变量（名字任意，比如$do/$go，这里用的是\$out），用起来会更省事，比如：export out="--dry-run=client -o yaml"

kubectl run ngx --image=nginx:alpine $out



命令 kubectl apply、kubectl delete 发送 HTTP 请求，管理 API 对象。





## 12 Pod:如何理解这个Kubernetes里最核心的概念



为了解决这样多应用联合运行的问题，同时还要不破坏容器的隔离，就需要在容器外面再建立一个“收纳舱”，让多个容器既保持相对独立，又能够小范围共享网络、存储等资源，而且永远是“绑在一起”的状态。所以，Pod 的概念也就呼之欲出了，容器正是“豆荚”里那些小小的“豌豆”，你可以在 Pod 的 YAML 里看到，**“spec.containers”字段其实是一个数组，里面允许定义多个容器。**







![img](https://static001.geekbang.org/resource/image/9e/75/9ebab7d513a211a926dd69f7535ac175.png?wh=1478x812)



不过这张图虽然很经典，参考价值很高，但毕竟有些年头了，随着 Kubernetes 的发展，它已经不能够全面地描述 Kubernetes 的资源对象了。受这张图的启发，我自己重新画了一份以 Pod 为中心的 Kubernetes 资源对象关系图，添加了一些新增的 Kubernetes 概念，今后我们就依据这张图来探索 Kubernetes 的各项功能。







![img](https://static001.geekbang.org/resource/image/b5/cf/b5a7003788cb6f2b1c5c4f6873a8b5cf.jpg?wh=1920x1298)





kubectl apply -f ngx-pod.yml

echo 'aaa' > a.txt
kubectl cp a.txt ngx-pod:/tmp

kubectl exec -it ngx-pod -- sh



虽然 Pod 是 Kubernetes 的核心概念，非常重要，但事实上在 Kubernetes 里通常并不会直接创建 Pod，因为它只是对容器做了简单的包装，比较脆弱，离复杂的业务需求还有些距离，需要 Job、CronJob、Deployment 等其他对象增添更多的功能才能投入生产使用。

我这里补充下个人遇到的坑，希望对大家有所帮助！ 当你使用kubectl apply -f指定YAML文件来创建pod时，只要你spec.containers.image里面的tag是latest，那么无论你的imagePullpolicy策略是什么，无论本地是否已存在该镜像文件，一定会先联网查询镜像信息，如果此时正好是私网无法连接互联网时，那么pod就会直接创建失败，你使用describe查询时报错也是无法拉取镜像。 只要tag不是latest，比如stable或者1.23什么的具体版本，本地已存在对应镜像文件，这时设置为IfNotPresent和Never才会生效，就可以在私网环境下愉快地离线创建pod。



## 13 Job/CronJob:为什么不直接用Pod来处理业务



在上次的课里我们学习了 Kubernetes 的核心对象 Pod，用来编排一个或多个容器，让这些容器共享网络、存储等资源，总是共同调度，从而紧密协同工作。因为 Pod 比容器更能够表示实际的应用，所以 Kubernetes 不会在容器层面来编排业务，而是把 Pod 作为在集群里调度运维的最小单位。



虽然面向对象的设计思想多用于软件开发，但它放到 Kubernetes 里却意外地合适。因为 Kubernetes 使用 YAML 来描述资源，把业务简化成了一个个的对象，内部有属性，外部有联系，也需要互相协作，只不过我们不需要编程，完全由 Kubernetes 自动处理（其实 Kubernetes 的 Go 语言内部实现就大量应用了面向对象）。面向对象的设计有许多基本原则，其中有两条我认为比较恰当地描述了 Kubernetes 对象设计思路，一个是“单一职责”，另一个是“组合优于继承”。





## 14｜ConfigMap/Secret：怎样配置、定制我的应用

ConfigMap/Secret首先你要知道，应用程序有很多类别的配置信息，但从数据安全的角度来看可以分成两类：

一类是明文配置，也就是不保密，可以任意查询修改，比如服务端口、运行参数、文件路径等等。

另一类则是机密配置，由于涉及敏感信息需要保密，不能随便查看，比如密码、密钥、证书等等。

这两类配置信息本质上都是字符串，只是由于安全性的原因，在存放和使用方面有些差异，所以 Kubernetes 也就定义了两个 API 对象，ConfigMap 用来保存明文配置，Secret 用来保存秘密配置。

kubectl get cm
kubectl describe cm info



kubectl apply  -f secret.yml
kubectl get secret
kubectl describe secret user



如何使用？

因为 ConfigMap 和 Secret 只是一些存储在 etcd 里的字符串，所以如果想要在运行时产生效果，就必须要以某种方式“注入”到 Pod 里，让应用去读取。在这方面的处理上 Kubernetes 和 Docker 是一样的，也是两种途径：**环境变量**和**加载文件**。

![img](https://static001.geekbang.org/resource/image/06/9d/0663d692b33c1dee5b08e486d271b69d.jpg?wh=1920x1661)![img](https://static001.geekbang.org/resource/image/9d/yy/9d3258da1f40554ae88212db2b4yybyy.jpg?wh=1920x1630)







## 15｜实战演练：玩转Kubernetes（1）

![img](https://static001.geekbang.org/resource/image/f4/05/f429ca7114eebf140632409f3fbcbb05.png?wh=1475x852)



CNI ：容器网络接口

CRI：容器运行时接口

CSI：容器存储接口





## 17｜更真实的云原生：实际搭建多节点的Kubernetes集群



不过在那之前，我们还需要有一个比 minikube 更真实的 Kubernetes 环境，它应该是一个多节点的 Kubernetes 集群，这样更贴近现实中的生产系统，能够让我们尽快地拥有实际的集群使用经验。所以在今天的这节课里，我们就来暂时忘掉 minikube，改用 kubeadm（https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/）搭建出一个新的 Kubernetes 集群，一起来看看更真实的云原生环境。





不需要写脚本来下载镜像啊，一条命令搞定： 



apt install -y kubeadm kubelet kubectl



kubeadm 的用法非常简单，只需要一个命令 kubeadm init 就可以把组件在 Master 节点上运行起来，不过它还有很多参数用来调整集群的配置，你可以用 -h 查看。这里我只说一下我们实验环境用到的 3 个参数：

--pod-network-cidr，设置集群里 Pod 的 IP 地址段。

--apiserver-advertise-address，设置 apiserver 的 IP 地址，对于多网卡服务器来说很重要（比如 VirtualBox 虚拟机就用了两块网卡），可以指定 apiserver 在哪个网卡上对外提供服务。

--kubernetes-version，指定 Kubernetes 的版本号。

sudo kubeadm init \
    --pod-network-cidr=10.10.0.0/16 \
    --apiserver-advertise-address=192.168.10.210



deb http://mirrors.ustc.edu.cn/kubernetes/apt kubernetes-xenial main







## 18 | Deployment：让应用永不宕机

所以 Deployment 和 Pod 实际上是一种松散的组合关系，Deployment 实际上并不“持有”Pod 对象，它只是帮助 Pod 对象能够有足够的副本数量运行，仅此而已。如果像 Job 那样，把 Pod 在模板里“写死”，那么其他的对象再想要去管理这些 Pod 就无能为力了。

好明白了这一点，那我们该用什么方式来描述 Deployment 和 Pod 的组合关系呢？

Kubernetes 采用的是这种“贴标签”的方式，通过在 API 对象的“metadata”元信息里加各种标签（labels），我们就可以使用类似关系数据库里查询语句的方式，筛选出具有特定标识的那些对象。**通过标签这种设计，Kubernetes 就解除了 Deployment 和模板里 Pod 的强绑定，把组合关系变成了“弱引用”。**







## 19｜Daemonset：忠实可靠的看门狗



今天我们就来看看另一类代表在线业务 API 对象：DaemonSet，它会在 Kubernetes 集群的每个节点上都运行一个 Pod，就好像是 Linux 系统里的“守护进程”（Daemon）



**为什么要有 DaemonSet**

但是有一些业务比较特殊，它们不是完全独立于系统运行的，而是与主机存在“绑定”关系，必须要依附于节点才能产生价值，比如说：

网络应用（如 kube-proxy），必须每个节点都运行一个 Pod，否则节点就无法加入 Kubernetes 网络。

监控应用（如 Prometheus），必须每个节点都有一个 Pod 用来监控节点的状态，实时上报信息。

日志应用（如 Fluentd），必须在每个节点上运行一个 Pod，才能够搜集容器运行时产生的日志数据。

安全应用，同样的，每个节点都要有一个 Pod 来执行安全审计、入侵检查、漏洞扫描等工作。



需要特别说明一下，“容忍度”并不是 DaemonSet 独有的概念，而是从属于 Pod，所以理解了“污点”和“容忍度”之后，你可以在 Job/CronJob、Deployment 里为它们管理的 Pod 也加上 tolerations，从而能够更灵活地调度应用。至于都有哪些污点、污点有哪些效果我就不细说了，Kubernetes 官网文档（https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/）上都列的非常清楚，在理解了工作原理之后，相信你自己学起来也不会太难。





**“静态 Pod”的 YAML 文件默认都存放在节点的** /etc/kubernetes/manifests 目录下，它是 Kubernetes 的专用目录。

你可以看到，Kubernetes 的 4 个核心组件 apiserver、etcd、scheduler、controller-manager 原来都以静态 Pod 的形式存在的，这也是为什么它们能够先于 Kubernetes 集群启动的原因。如果你有一些 DaemonSet 无法满足的特殊的需求，可以考虑使用静态 Pod，编写一个 YAML 文件放到这个目录里，节点的 kubelet 会定期检查目录里的文件，发现变化就会调用容器运行时创建或者删除静态 Pod。



## 20｜Service：微服务架构的应对之道







![img](https://static001.geekbang.org/resource/image/03/74/0347a0b3bae55fb9ef6c07469e964b74.png?wh=1622x1214)

**这里 Service 使用了 iptables 技术**.不过 Service 并不是只能使用 iptables 来实现负载均衡，它还有另外两种实现技术：**性能更差的 userspace** 和**性能更好的 ipvs**，但这些都属于底层细节，我们不需要刻意关注。

而且 Service 对象的 IP 地址还有一个特点，它是一个“虚地址”，不存在实体，只能用来转发流量。会发现根本 ping 不通，因为 Service 的 IP 地址是“虚”的，只用于转发流量，所以 ping 无法得到回应数据包，也就失败了。

![img](https://static001.geekbang.org/resource/image/73/68/7370727f61e82f96acf0316456329968.jpg?wh=1920x2465)





**为什么 Service 的 IP 地址是静态且虚拟的？出于什么目的，有什么好处？**

svc是基于内核的netfilter技术实现的，在用户态通过iptable和ipvs应用hook链和表，所以它的IP注定是虚拟的，至于静态考虑则是为暴露的服务提供相对稳定性能的dns解析

**你了解负载均衡技术吗？它都有哪些算法，Service 会用哪种呢？**

负载均衡技术分为DNS、四层和七层，dns也是一种简单的负载均衡只是算法很简单随机；四层负载均衡主要有nat、IP隧道等，主要是做一些IP和端口的变换，算法也不多比如rr，七层负载均衡则是基于http header来做负载算法很多，可以做一些流量控制；其次还有在客户端做负载均衡比如nacos、istio等



## 21｜Ingress：集群进出流量的总管

Service 很有用，但也只能说是“基础设施”，它对网络流量的管理方案还是**太简单**，离复杂的现代应用架构需求还有很大的差距，所以 Kubernetes 就在 Service 之上又提出了一个新的概念：Ingress。



不过除了七层负载均衡，这个对象还应该承担更多的职责，也就是作为流量的总入口，统管集群的进出口数据，“扇入”“扇出”流量（也就是我们常说的“南北向”），让外部用户能够安全、顺畅、便捷地访问内部服务（图片来源）：

![img](https://static001.geekbang.org/resource/image/e6/55/e6ce31b027ba2a8d94cdc553a2c97255.png?wh=1288x834)

所以，这个 API 对象就顺理成章地被命名为 Ingress，意思就是集群内外边界上的入口





Ingress 可以说是在七层上另一种形式的 Service，它同样会代理一些后端的 Pod，也有一些路由规则来定义流量应该如何分配、转发，只不过这些规则都使用的是 HTTP/HTTPS 协议。

你应该知道，Service 本身是没有服务能力的，它只是一些 iptables 规则，真正配置、应用这些规则的实际上是节点里的 kube-proxy 组件。如果没有 kube-proxy，Service 定义得再完善也没有用。

同样的，Ingress 也只是一些 HTTP 路由规则的集合，相当于一份静态的描述文件，真正要把这些规则在集群里实施运行，还需要有另外一个东西，这就是 Ingress Controller，它的作用就相当于 Service 的 kube-proxy，能够读取、应用 Ingress 规则，处理、调度流量。

不过 Ingress Controller 要做的事情太多，与上层业务联系太密切，所以 Kubernetes 把 Ingress Controller 的实现交给了社区，任何人都可以开发 Ingress Controller，只要遵守 Ingress 规则就好。





![img](https://static001.geekbang.org/resource/image/eb/f8/ebebd12312fa5e6eb1ea90c930bd5ef8.png?wh=1920x706)











![img](https://static001.geekbang.org/resource/image/6b/af/6bd934a9c8c81a9f194d2d90ede172af.jpg?wh=1920x1005)





![img](https://static001.geekbang.org/resource/image/bb/14/bb7a911e10c103fb839e01438e184914.jpg?wh=1920x736)



![img](https://static001.geekbang.org/resource/image/6a/08/6a373b5b5e8c0869f6b77bc8d5b35708.jpg?wh=1920x2856)



## 24｜PersistentVolume：怎么解决数据持久化的难题？

介绍 ConfigMap/Secret 的时候，我们就遇到过 Kubernetes 里的 Volume 存储卷的概念，它使用字段 volumes 和 volumeMounts，相当于是给 Pod 挂载了一个“虚拟盘”，把配置信息以文件的形式注入进 Pod 供进程使用。



**作为存储的抽象，PV 实际上就是一些存储设备、文件系统**，比如 Ceph、GlusterFS、NFS，甚至是本地磁盘，管理它们已经超出了 Kubernetes 的能力范围，所以，一般会由系统管理员单独维护，然后再在 Kubernetes 里创建对应的 PV。

要注意的是，PV 属于集群的系统资源，是和 Node 平级的一种对象，Pod 对它没有管理权，只有使用权。

这么多种存储设备，只用一个 PV 对象来管理还是有点太勉强了，不符合“单一职责”的原则，让 Pod 直接去选择 PV 也很不灵活。于是 Kubernetes 就又增加了两个新对象，**PersistentVolumeClaim 和 StorageClass**，用的还是“中间层”的思想，把存储卷的分配管理过程再次细化。

**PersistentVolumeClaim**，简称 PVC，从名字上看比较好理解，就是用来向 Kubernetes 申请存储资源的。**PVC 是给 Pod 使用的对象，它相当于是 Pod 的代理，代表 Pod 向系统申请 PV。**一旦资源申请成功，Kubernetes 就会把 PV 和 PVC 关联在一起，这个动作叫做“绑定”（bind）。



但是，系统里的存储资源非常多，如果要 PVC 去直接遍历查找合适的 PV 也很麻烦，所以就要用到 StorageClass。

![img](https://static001.geekbang.org/resource/image/a4/d8/a4d709808a0ef729604c884c50748bd8.jpg?wh=1920x1310)

不过还有一点小问题，因为这个 PV 是 HostPath 类型，只在本节点存储，如果 Pod 重建时被调度到了其他节点上，那么即使加载了本地目录，也不会是之前的存储位置，持久化功能也就失效了。

不过还有一点小问题，因为这个 PV 是 HostPath 类型，只在本节点存储，如果 Pod 重建时被调度到了其他节点上，那么即使加载了本地目录，也不会是之前的存储位置，持久化功能也就失效了。



## 25｜PersistentVolume + NFS：怎么使用网络共享存储？





![image-20220920112248135](/Users/yinqc/Library/Application Support/typora-user-images/image-20220920112248135.png)











这个在 Kubernetes 里就是“动态存储卷”的概念，它可以用 StorageClass 绑定一个 Provisioner 对象，而这个 Provisioner 就是一个能够自动管理存储、创建 PV 的应用，代替了原来系统管理员的手工劳动。

Provisioner: 供应品

![img](https://static001.geekbang.org/resource/image/e3/1e/e3905990be6fb8739fb51a4ab9856f1e.jpg?wh=1920x856)



https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class-1





## 26｜StatefulSet：怎么管理有状态的应用？

![image-20220920123513619](/Users/yinqc/Library/Application Support/typora-user-images/image-20220920123513619.png)





## 27 | 滚动更新

Kubernetes 如何定义应用版本

在 Kubernetes 里，版本更新使用的不是 API 对象，而是两个命令：kubectl apply 和 kubectl rollout，当然它们也要搭配部署应用所需要的 Deployment、DaemonSet 等 YAML 文件。



不过在我们信心满满开始操作之前，首先要理解在 Kubernetes 里，**所谓的“版本”到底是什么？**

我们常常会简单地认为“版本”就是应用程序的“版本号”，或者是容器镜像的“标签”，但不要忘了，在 Kubernetes 里应用都是以 Pod 的形式运行的，而 Pod 通常又会被 Deployment 等对象来管理，所以应用的“版本更新”实际上更新的是整个 Pod。

那 Pod 又是由什么来决定的呢？

仔细回忆一下之前我们创建的那么多个对象，你就会发现，Pod 是由 YAML 描述文件来确定的，更准确地说，是 Deployment 等对象里的字段 template。

**所以，在 Kubernetes 里应用的版本变化就是 template 里 Pod 的变化**，哪怕 template 里只变动了一个字段，那也会形成一个新的版本，也算是版本变化。



## 28｜应用保障：如何让Pod运行得更健康？

作为 Kubernetes 里的核心概念和原子调度单位，Pod 的主要职责是管理容器，以逻辑主机、容器集合、进程组的形式来代表应用，它的重要性是不言而喻的。



**容器资源配额**

那么今天我们回过头来，在之前那些上层 API 对象的基础上，一起来看看在 Kubernetes 里配置 Pod 的两种方法：**资源配额 Resources、检查探针 Probe**，它们能够给 Pod 添加各种运行保障，让应用运行得更健康。

“requests”，意思是容器要申请的资源，也就是说要求 Kubernetes 在创建 Pod 的时候必须分配这里列出的资源，否则容器就无法运行。

“limits”，意思是容器使用资源的上限，不能超过设定值，否则就有可能被强制停止运行。



不过 CPU 时间也不能无限分割，Kubernetes 里 CPU 的最小使用单位是 0.001，为了方便表示用了一个特别的单位 m，也就是“milli”“毫”的意思，比如说 500m 就相当于 0.5。



**什么是容器状态探针**

所以，我们必须把应用变成灰盒子，让部分内部信息对外可见，这样 Kubernetes 才能够探查到内部的状态。

**Startup**，启动探针，用来检查应用是否已经启动成功，适合那些有大量初始化工作要做，启动很慢的应用。

**Liveness**，存活探针，用来检查应用是否正常运行，是否存在死锁、死循环。

**Readiness**，就绪探针，用来检查应用是否可以接收流量，是否能够对外提供服务。

你需要注意这三种探针是递进的关系：应用程序先启动，加载完配置文件等基本的初始化数据就进入了 Startup 状态，之后如果没有什么异常就是 Liveness 存活状态，但可能有一些准备工作没有完成，还不一定能对外提供服务，只有到最后的 Readiness 状态才是一个容器最健康可用的状态。



![img](https://static001.geekbang.org/resource/image/ea/84/eaff5e640171984a4b1b2285982ee184.jpg?wh=1920x1000)

![img](https://static001.geekbang.org/resource/image/64/d9/64fde55dd2eab68f9968ff34218646d9.jpg?wh=1920x1200)



## 29｜集群管理：如何用名字空间分隔系统资源？

首先要明白，**Kubernetes 的名字空间并不是一个实体对象，只是一个逻辑上的概念。**它可以把集群切分成一个个彼此独立的区域，然后我们把对象放到这些区域里，就实现了类似容器技术里 namespace 的隔离效果，应用只能在自己的名字空间里分配资源和运行，不会干扰到其他名字空间里的应用。





Kubernetes 初始化集群的时候也会预设 4 个名字空间：default、kube-system、kube-public、kube-node-lease。我们常用的是前两个，default 是用户对象默认的名字空间，kube-system 是系统组件所在的名字空间，相信你对它们已经很熟悉了。

ResourceQuota 对象可以为名字空间添加资源配额，限制全局的 CPU、内存和 API 对象数量。

这个时候就要用到一个很小但很有用的辅助对象了—— **LimitRange**，简称是 limits，它能为 API 对象添加默认的资源配额限制。





## 30｜系统监控：如何使用Metrics Server和Prometheus？

**Metrics Server**

Kubernetes 也提供了类似的命令，就是 kubectl top，不过默认情况下这个命令不会生效，必须要安装一个插件 Metrics Server 才可以。



![img](https://static001.geekbang.org/resource/image/8f/9e/8f4a22788c03b06377cabe791c67989e.png?wh=1562x572)



**HorizontalPodAutoscaler水平自动伸缩**

HorizontalPodAutoscaler 的能力完全基于 Metrics Server，它从 Metrics Server 获取当前应用的运行指标，主要是 CPU 使用率，再依据预定的策略增加或者减少 Pod 的数量。



**Prometheus**

其实，Prometheus 的历史比 Kubernetes 还要早一些，它最初是由 Google 的离职员工在 2012 年创建的开源项目，灵感来源于 Borg 配套的 BorgMon 监控系统。后来在 2016 年，Prometheus 作为第二个项目加入了 CNCF，并在 2018 年继 Kubernetes 之后顺利毕业，成为了 CNCF 的不折不扣的“二当家”，也是云原生监控领域的“事实标准”。



下面的这张图是 Prometheus 官方的架构图:

![img](https://static001.geekbang.org/resource/image/e6/64/e62cebb3acc995246f203d698dfdc964.png?wh=1351x811)

在 Prometheus Server 之外还有三个重要的组件：

Push Gateway，用来适配一些特殊的监控目标，把默认的 Pull 模式转变为 Push 模式。

Alert Manager，告警中心，预先设定规则，发现问题时就通过邮件等方式告警。

Grafana 是图形化界面，可以定制大量直观的监控仪表盘。



## 31｜网络通信：CNI是怎么回事？又是怎么工作的？

早在“入门篇”的第 6 讲里，我们就简单介绍过 Docker 的网络模式，然后在“中级篇”的第 17 讲，我们又为 Kubernetes 安装了一个网络插件 Flannel。这些都与网络相关，但也只是浅尝辄止，并没有太多深究。



如果你是一个喜欢刨根问底的人，会不会很好奇：Flannel 到底是如何工作的呢？它为什么能够让 Kubernetes 集群正常通信呢？还有没有其他网络插件呢？

今天我们就来聊一下这个话题，讲讲 Kubernetes 的网络接口标准 CNI，以及 Calico、Cilium 等性能更好的网络插件。



**Kubernetes 的网络模型**

在学习 Kubernetes 的网络之前，我们还是要先简单回顾一下 Docker 的网络知识。

你对 Docker 的 null、host 和 bridge 三种网络模式还有印象吗？这里我重新画了一张图，描述了 Docker 里最常用的 bridge 网络模式.

Docker 会创建一个名字叫“docker0”的网桥，默认是私有网段“172.17.0.0/16”。每个容器都会创建一个虚拟网卡对（**veth pair**），两个虚拟网卡分别“插”在容器和网桥上，这样容器之间就可以互联互通了。Docker 的网络方案简单有效，但问题是它只局限在单机环境里工作，跨主机通信非常困难（需要做端口映射和网络地址转换）。

针对 Docker 的网络缺陷，Kubernetes 提出了一个自己的网络模型“**IP-per-pod**”，能够很好地适应集群系统的网络需求，它有下面的这 4 点基本假设：

- 集群里的每个 Pod 都会有唯一的一个 IP 地址。

- Pod 里的所有容器共享这个 IP 地址。

- 集群里的所有 Pod 都属于同一个网段。

- Pod 直接可以基于 IP 地址直接访问另一个 Pod，不需要做麻烦的网络地址转换（NAT）。

这个网络让 Pod 摆脱了主机的硬限制，是一个“平坦”的网络模型，很好理解，通信自然也非常简单。

因为 Pod 都具有独立的 IP 地址，相当于一台虚拟机，而且直连互通，也就可以很容易地实施域名解析、负载均衡、服务发现等工作，以前的运维经验都能够直接使用，对应用的管理和迁移都非常友好。



**什么是 CNI**

Kubernetes 定义的这个网络模型很完美，但要把这个模型落地实现就不那么容易了。所以 Kubernetes 就专门制定了一个标准：CNI（Container Networking Interface）。



CNI 为网络插件定义了一系列通用接口，开发者只要遵循这个规范就可以接入 Kubernetes，为 Pod 创建虚拟网卡、分配 IP 地址、设置路由规则，最后就能够实现“IP-per-pod”网络模型。



依据实现技术的不同，CNI 插件可以大致上分成“Overlay”“Route”和“Underlay”三种。

Overlay 的原意是“覆盖”，是指它构建了一个工作在真实底层网络之上的“逻辑网络”，把原始的 Pod 网络数据封包，再通过下层网络发送出去，到了目的地再拆包。因为这个特点，它对底层网络的要求低，适应性强，缺点就是有额外的传输成本，性能较低。

Route 也是在底层网络之上工作，但它没有封包和拆包，而是使用系统内置的路由功能来实现 Pod 跨主机通信。它的好处是性能高，不过对底层网络的依赖性比较强，如果底层不支持就没办法工作了。

Underlay 就是直接用底层网络来实现 CNI，也就是说 Pod 和宿主机都在一个网络里，Pod 和宿主机是平等的。它对底层的硬件和网络的依赖性是最强的，因而不够灵活，但性能最高。





Flannel（https://github.com/flannel-io/flannel/）由 CoreOS 公司（已被 Redhat 收购）开发，最早是一种 Overlay 模式的网络插件，使用 UDP 和 VXLAN 技术，后来又用 Host-Gateway 技术支持了 Route 模式。Flannel 简单易用，是 Kubernetes 里最流行的 CNI 插件，但它在性能方面表现不是太好，所以一般不建议在生产环境里使用。



现在还有两个常用 CNI 插件：Calico、Cilium，我们做个简略的介绍。

Calico（https://github.com/projectcalico/calico）是一种 Route 模式的网络插件，使用 BGP 协议（Border Gateway Protocol）来维护路由信息，性能要比 Flannel 好，而且支持多种网络策略，具备数据加密、安全隔离、流量整形等功能。

Cilium（https://github.com/cilium/cilium）是一个比较新的网络插件，同时支持 Overlay 模式和 Route 模式，它的特点是深度使用了 Linux eBPF 技术，在内核层次操作网络数据，所以性能很高，可以灵活实现各种功能。在 2021 年它加入了 CNCF，成为了孵化项目，是非常有前途的 CNI 插件。



**CNI 插件是怎么工作的**

Flannel 比较简单，我们先以它为例看看 CNI 在 Kubernetes 里的工作方式。