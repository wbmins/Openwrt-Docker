## 镜像使用方法

1、打开网卡混杂模式，其中eth0根据ifconfig命令找到自己的本地网卡名称替换
```
sudo ip link set enp1s0 promisc on
```
2、创建名称为macvlan的虚拟网卡，并指定网关gateway、子网网段subnet、虚拟网卡的真实父级网卡parent（第一步中的本地网卡名称）
```
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=enp1s0 macnet
```
3、查看虚拟网卡是否创建成功，成功的话能看到名称为“macnet”的虚拟网卡
```
docker network ls
```
4、拉取镜像，可以通过阿里云镜像提升镜像拉取速度
```
docker pull wbmins/openwrt:latest
```
5、创建容器并后台运行
```
docker run --restart always --name openwrt -d --network macnet --privileged wbmins/openwrt /sbin/init
```
6、进入容器内部环境
```
docker exec -it openwrt bash
```
7、根据自己实际情况修改网络配置，修改完成后保存配置
```
vi /etc/config/network
```
8、退出容器内部环境，在宿主机环境执行重启容器命令
```
docker container restart openwrt
```

## 鸣谢

SuLingGG/OpenWrt-Docker:

<https://github.com/SuLingGG/OpenWrt-Docker>

ImmortalWrt OpenWrt Source:

<https://github.com/immortalwrt/immortalwrt>

P3TERX/Actions-OpenWrt:

<https://github.com/P3TERX/Actions-OpenWrt>

OpenWrt Source Repository:

<https://github.com/openwrt/openwrt>

Lean's OpenWrt source:

<https://github.com/coolsnowwolf/lede>
