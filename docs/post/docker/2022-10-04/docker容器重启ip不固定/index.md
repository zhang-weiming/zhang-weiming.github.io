# Docker容器重启IP不固定


docker 容器重启后IP可能会变...

<!--more-->

## 原因解析

这里需要借助 docker 和 Linux 的网桥来给容器设置固定网络下的IP。

docker 有默认的网桥设置，默认网桥叫 docker0，可以借助命令 `drctl show` 来查看，网段在172.17.0.0/16。

## 解决

1. 首先创建一个 docker network
	``` sh
	# xxx.xxx.xxx.xxx/16 对应的子网掩码为 255.255.0.0
	# xxx.xxx.xxx.xxx/24 对应的子网掩码为 255.255.255.0
	docker network create --driver bridge --subnet 192.168.0.0/24 --gateway 192.168.0.1 mynet
	```
2. 下面操作二选一
    + 停止并删除正在运行的容器，重新创建容器，在创建容器的 `docker run ...` 命令中加入参数 `--network mynet`
    + 将某个容器加入到指定的网络中，使用命令 `docker network connect <networkName> <containerName>`

## 参考

- [Docker网络互联原理及自定义网络的使用](https://www.jianshu.com/p/d4bb218ec465)
- [Docker网络](https://www.cnblogs.com/niujifei/p/16750149.html)
- [Pipework网络方案](https://blog.csdn.net/u013355826/article/details/97399078)


---

> 作者: [张大锅](https://zhang-weiming.github.io/)  
> URL: https://zhang-weiming.github.io/post/docker/2022-10-04/docker%E5%AE%B9%E5%99%A8%E9%87%8D%E5%90%AFip%E4%B8%8D%E5%9B%BA%E5%AE%9A/  

