[![Docker Images](https://github.com/wbmins/ImmortalWrt-Docker/actions/workflows/build_docker_images.yml/badge.svg)](https://github.com/wbmins/ImmortalWrt-Docker/actions/workflows/build_docker_images.yml)
[![Docker Pulls](https://img.shields.io/docker/pulls/wbmins/openwrt.svg?label=Docker%20Pulls&logo=docker&color=orange)](https://hub.docker.com/r/wbmins/openwrt)
## 使用方法
1、打开网卡混杂模式，其中enp1s0根据ifconfig命令找到自己的本地网卡名称替换
```
sudo ip link set enp1s0 promisc on
```
2、docker-compose 启动
```yaml
services:
  openwrt:
    image:  wbmins/openwrt:x86_64  
    container_name: openwrt
    privileged: true
    restart: always
    networks:
      macnet

networks:
  macnet:
    driver: macvlan
    driver_opts:
      parent: enp1s0 # 第一步的网卡
    ipam:
      config:
        - subnet: 192.168.1.0/24 #子网网段
          gateway: 192.168.1.1 #网关
```

3、进入容器内部环境
```
docker exec -it openwrt ash
```
4、根据自己实际情况修改网络配置，修改完成后保存配置
```
vi /etc/config/network
```
5、退出容器内部环境，在宿主机环境执行重启容器命令
```
docker container restart openwrt
```

## 其他 docker 容器如何使用代理
|名字|	ip	|接口|
| ----------- | ----------- |----------- |
|宿主机|	192.168.1.4|	enp1s0|
|Openwrt 容器|	192.168.1.5|	macvlan|

1、创建桥接 br-macvlan 网络 `ip link add br-macvlan link enp1s0 type macvlan mode bridge`

2、br-macvlan 网络指定 ip `ip addr add 192.168.1.111 dev br-macvlan`

3、启用 br-macvlan 网络 `ip link set br-macvlan up`

4、让 Openwrt 容器网络经过 br-macvlan `ip route add 192.168.1.5 dev br-macvlan`

5、容器启动指定代理 `-e http_proxy=http://192.168.1.5:7890 -e https_proxy=http://192.168.1.5:7890`

6、上述配置重启后失效，如果需要可以写一个脚本开机自启设置

7、参考
  - [macvlan网络模式下容器与宿主机互通](https://rehtt.com/index.php/archives/236/)
  - [Docker 部署的 openWrt 软路由, 并解决无法与宿主机通信问题](https://www.treesir.pub/post/n1-docker)

## 鸣谢
- [OpenWrt-Docker](https://github.com/SuLingGG/OpenWrt-Docker)
- [SuLingGG/OpenWrt-Docker](https://github.com/SuLingGG/OpenWrt-Docker)
- [ImmortalWrt OpenWrt Source](https://github.com/immortalwrt/immortalwrt)
- [P3TERX/Actions-OpenWrt](https://github.com/P3TERX/Actions-OpenWrt)
- [OpenWrt Source Repository](https://github.com/openwrt/openwrt)
- [Lean's OpenWrt source](https://github.com/coolsnowwolf/lede)
