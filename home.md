---
title: openwrt路由器的一些配置
description: 111111
published: true
date: 2024-10-16T03:47:44.243Z
tags: 
editor: markdown
dateCreated: 2024-10-13T04:24:50.897Z
---




路由器的综合配置 方式 


连接的具体模式 表示 的内容




vi /etc/config/network

查看路由器的各个配置





可以查看各个结构的链路连接情况 

如果 `192.168.123.1` 是 **上游网络（WAN 或 WAN2）** 的 DNS 服务器，并且你希望通过 **LAN 口** 将这个上游 DNS 服务器通告给连接到 LAN 的客户端，你可以通过以下步骤进行配置。

### 配置 DHCP 选项通告上游 DNS

1. **编辑 DHCP 配置** 打开 `/etc/config/dhcp` 文件，确保在 `lan` 接口下添加 DHCP 选项，将上游 DNS 服务器 `192.168.123.1` 通告给 LAN 上的客户端：

   ```
   bash
   
   
   Copy code
   vi /etc/config/dhcp
   ```

2. **在 LAN 配置中添加 DNS 通告** 在 `config dhcp 'lan'` 部分，添加以下内容：

   ```
   bashCopy codeconfig dhcp 'lan'
       option interface 'lan'
       option start '100'
       option limit '150'
       option leasetime '12h'
       option dhcpv6 'server'
       option ra 'server'
       option dhcp_option '6,192.168.123.1'
   ```

   这将确保 OpenWrt 的 LAN 接口通过 DHCP 把上游网络的 DNS 服务器 `192.168.123.1` 通告给连接到 LAN 的客户端。

3. **保存并重启服务** 保存文件后，重启 DNS 和 DHCP 服务：

   ```
   bash
   
   
   Copy code
   /etc/init.d/dnsmasq restart
   ```

### 确保路由器使用上游 DNS

为了确保 OpenWrt 路由器自身也使用 `192.168.123.1` 作为上游 DNS，你可以在 WAN 接口（假设为 `lan2`）的配置中明确设置 DNS 服务器。

1. **编辑网络配置** 打开 `/etc/config/network` 文件：

   ```
   bash
   
   
   Copy code
   vi /etc/config/network
   ```

2. **检查 WAN 接口的 DNS 设置** 确保在 `wan` 接口中，DNS 设置为 `192.168.123.1`：

   ```
   bashCopy codeconfig interface 'wan'
       option proto 'dhcp'
       option device 'lan2'
       option peerdns '0'
       list dns '192.168.123.1'
   ```

   如果你还有 `wan2` 接口（例如 `lan3`），也可以类似设置。

3. **保存并重启网络** 重启网络服务：

   ```
   bash
   
   
   Copy code
   /etc/init.d/network restart
   ```

### 验证配置

1. **客户端检查** 在 LAN 口的客户端，检查是否正确获取了 `192.168.123.1` 作为 DNS 服务器：

   - Linux 或 Mac：运行 `cat /etc/resolv.conf` 查看是否有 `192.168.123.1`。
   - Windows：运行 `ipconfig /all`，检查 DNS 设置。

2. **路由器自身 DNS 检查** 检查 OpenWrt 路由器自身是否使用了上游 DNS：

   ```
   bash
   
   
   Copy code
   cat /tmp/resolv.conf.auto
   ```

   这应该显示 `192.168.123.1` 或其他手动配置的 DNS 服务器。

通过这种方式，LAN 口的客户端会自动获取上游 DNS (`192.168.123.1`)，确保所有设备都能通过上游网络解析域名。





4o



有些服务不支持热重载 ，这样允许连接



我接入了两个wan 口 但是 只有  一个 wan 口 有 Adguardhome 的应用 



我把 dns 的 服务器 的wan 口 l







uci set network.wan.peerdns='0'
uci add_list network.wan.dns='8.8.8.8'
uci add_list network.wan.dns='1.1.1.1'
uci commit network
/etc/init.d/network restart



dns msq 

停止 本地dns 功能 ，其 功能 请求 上级 的 dns 解决 





在网桥那边设置高级的设置 ，网速发生改变

![image-20241014155119614](/笔记/image-20241014155119614.png)



在openwrt 当中 dnsmsq 的只能方式 每次会重读 /etc/resolv.conf 文件中的内容 ，每次重写后 内容 就会 发生改变

并且在 本地dns服务器中寻找



关于判定上游路由器的配置 



cat /etc/config/dhcp

控制 dnsmasq的详细参数 

控制lan口 的上游dns 服务器 



config dnsmasq
        option domainneeded '1'
        option localise_queries '1'
        option rebind_protection '0'
        option local '/lan/'
        option domain 'lan'
        option expandhosts '1'
        option cachesize '0'
        option readethers '1'
        option leasefile '/tmp/dhcp.leases'
        option localservice '1'
        option mini_ttl '3600'
        option ednspacket_max '1232'
        option port '0'
        option nohosts '1'
        option allservers '1'
        option quietdhcp '1'
        option filter_aaaa '1'
        option nonegcache '1'
        option noresolv '1'

config dhcp 'lan'
        option interface 'lan'
        option start '100'
        option limit '150'
        option leasetime '12h'
        option dhcpv4 'server'
        option dhcpv6 'hybrid'
        option ra 'hybrid'
        list ra_flags 'managed-config'
        list ra_flags 'other-config'
        option ndp 'hybrid'
        option ra_management '1'
        list dhcp_option '6,192.168.123.1'

config odhcpd 'odhcpd'
        option maindhcp '0'
        option leasefile '/tmp/hosts/odhcpd'
        option leasetrigger '/usr/sbin/odhcpd-update'
        option loglevel '4'





  list dhcp_option '6,192.168.123.1'

  option noresolv '1'



判断是没有连接wan 口 仅连接了lan1 和 lan2 



radio0' is disabled

该选项表示无线网卡被禁用



在lan 口 设置 了上游 dns 的 请求 方式

  list dhcp_option '6,192.168.123.1'

路由器本身只做代理请求， dns 解析由上级路由器完成 



/etc/resolve.conf 

查看的默认的dns 服务器 的配置 

但是在 openwrt 内部 dns 的 转发会受到

已经受到 DHCP dnsmasp 的接管了 ，我配置完 lan口的dns 服务器不对配置产生影响 



在 dnsmasq 的配置选项当中 ，我已经进制dnsmasq的基本属性配置 

路由器此时仅起到代理请求转发的作用 

dns本地不解析，请求转发给上级路由器 

年龄 

   option ignore '1' 

忽视dns 内部解析的功能 



配置 lan 口的dns 配置 







root@360T7:~# opkg update
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/targets/mediatek/filogic/packages/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_core
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/targets/mediatek/filogic/packages/Packages.sig
Signature check passed.
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/base/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_base
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/base/Packages.sig

Signature check passed.
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/luci/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_luci
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/luci/Packages.sig
Signature check passed.
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/packages/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_packages
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/packages/Packages.sig
Signature check passed.
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/routing/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_routing
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/routing/Packages.sig
Signature check passed.
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/telephony/Packages.gz
Updated list of available packages in /var/opkg-lists/immortalwrt_telephony
Downloading https://mirrors.vsean.net/openwrt/releases/23.05.1/packages/aarch64_cortex-a53/telephony/Packages.sig



给lan 配置 一条 dns 路由器 

reboot 路由 器 后 正常访问网络 

软件包可以正常更新 



两个wan 口 还是 插在 lan 口 上 ，软件包 可以正常更新 





在dhcp 配置了一条单线路  指定 对应 的DHCP 服务器材



重启后 resolve.conf 中的配置文件被接管 ，

并且写入了新的信息



# Interface lan

nameserver 192.168.123.1

# Interface wan2

nameserver 223.5.5.5
nameserver 114.114.114.114
search lan



仅重启/etc/init.d/network 

网络resolve 文件的内容不会更新 



路由器网络重启后， 内部的配置发生改变 



前几次都是被dnsmasq 拦截 重新定义了 reosvl文件的内容 

​        option noresolv '1'

     option ignore '1'
        list dhcp_option '6,192.168.123.1'


停止 dnsmasq服务器的拦截活动 



![image-20241014172136421](/笔记/image-20241014172136421.png)

双wan 的配置并不能增加网络的原有速度

现在配置 mwan 3 负载 均衡 



设备要使用对应的eth设备



现在的设计是统一使用上行的adguardhome 





分布式



在子网络中配置dns



配置 lan 的dns 请求服务器 





链路聚合的方式 

厨房间bond接口 







## 交换机端口的配置表

![image-20241015160554907](/笔记/image-20241015160554907.png)



Access Trunk 两种标签



交换机内部的流量都是带标签的



tag 标签设置流量出流量和



tag untag pvid





一个是否又pvid 分为两种情况 ，trunk 





## 交换机 

广播会在不同的端口上泛洪



无标签处理就会直接丢弃 



## vlan id

vid 



trunk

内部到达的流量 ，

vlan 1 

vlan 2 

vlan 3



一个端口属于多个uid

交换机内部的流量

 





交换机外部的流量 



![image-20241015161802561](/笔记/image-20241015161802561.png)

 

端口的配置要求表示的内容 

![image-20241015161924736](/笔记/image-20241015161924736.png)



一个端口最多 有一个  pvid 和 vid

![image-20241015162030187](/笔记/image-20241015162030187.png)



检查确认端口 

![image-20241015162556282](/笔记/image-20241015162556282.png)

设置对应的pvid 然后对于pvid 进行调节

![image-20241015162722360](/笔记/image-20241015162722360.png)





![image-20241015162743338](/笔记/image-20241015162743338.png)



添加 

检查确认端口 ，添加 vlan ，设置 acess端口 ，设置trunk 端口 

标签指的是 对应 的 vlan id 





由于新版openwrt固件的交换机设置换成了dsa模式，原有的swconfig 没有了， 在接口菜单上就没有了交换机选项。

DSA驱动与swconfig驱动介绍
DSA 代表分布式交换机架构，是用于网络交换机的 Linux 内核子系统。它是 OpenWrt 的 swconfig 框架的上游替代品，许多新路由器使用 DSA 驱动程序而不是 swconfig 驱动程序。
在 DSA 中，每个交换机端口都是一个单独的 Linux 接口。这意味着ip/ifconfig命令将显示接口等lan1，lan2，wan等。
DSA 交换机端口可以用作独立接口（WAN 的通用解决方案），也可以使用 Linux 桥接接口进行桥接。在后一种情况下，交换机仍然能够在硬件级别路由流量，因此不会影响性能。
每个端口最多只能是一个网桥的一部分



在dsa 当中 wan 的 通用解决方案 













######  Table of Contents 

- VLAN
  - [通过多数OpenWrt路由器默认方案来解释VLAN](https://openwrt.org/zh/docs/guide-user/network/vlan/switch_configuration#通过多数openwrt路由器默认方案来解释vlan)
  - [如何确认设备是否集成支持VLAN的硬件交换机？](https://openwrt.org/zh/docs/guide-user/network/vlan/switch_configuration#如何确认设备是否集成支持vlan的硬件交换机)
  - [Assigning VLAN IDs on VLAN-enabled switch hardware](https://openwrt.org/zh/docs/guide-user/network/vlan/switch_configuration#assigning_vlan_ids_on_vlan-enabled_switch_hardware)
  - [创建驱动级VLAN](https://openwrt.org/zh/docs/guide-user/network/vlan/switch_configuration#创建驱动级vlan)

# VLAN

VLAN是**V**irtual **L**ocal **A**rea **N**etwork（即：虚拟局域网）首个字母的缩写，它是物理网络交换设备在OSI第2层(即：数据链路层)上的虚拟隔断。

它是一种无需配置完整的子网和路由却能隔离即便使用了同一个物理网络客户端的方法，工作原理是在网络流量中添加一个标签（VLAN ID）并使用这个标签来决定流量路径进而将客户端分隔在不同VLAN中。要使用VLAN，您需要至少2个支持VLAN功能的设备（任何路由都需要至少2端），通常为高级路由器、任何运行OpenWrt的设备、任何自响应的PC，或者单板电脑。(Windows，MacOS，Linux和BSDs均支持VLAN)

OpenWrt支持 [IEEE 802.1Q](https://en.wikipedia.org/wiki/IEEE_802.1Q) 和 [IEEE 802.1ad](https://en.wikipedia.org/wiki/IEEE_802.1ad) VLAN标准。

许多多端口嵌入式设备都包含支持VLAN的交换机（比如：所有带WAN口的路由器都包含支持VLAN的交换机），单端口设备和每个端口都有以太网控制器的设备（例如PC开发板或一般大多数PC硬件）则由操作系统驱动程序管理VLAN。

对于集成硬件VLAN交换机的设备，集成VLAN交换机一般会被连接到设备（比如：路由器）的内部以太网接口上，或多或少独立于设备（比如：路由器）的主CPU而执行交换任务。 可以通过修改设备集成VLAN交换机的配置选项，为一些端口配置相同的VLAN ID，从而把这些端口置于同一个VLAN当中，处于同一个VLAN当中的设备能够互相通信。 需要注意的是，设备集成VLAN交换机的端口往往不仅包含可见的WAN、LAN等物理网口，可能设备内部还有从交换机端口到内置以太网接口的连接（一般为交换机到路由器CPU的连接），在这种情况下，配置VLAN时也需要正确配置CPU接口，以免无法正常访问设备的CPU接口。

具有软件VLAN支持的设备通常具有多个以太网控制器，如果想要把两个接口置于同一个VLAN，以允许他们（如同位于一个硬件实现的VLAN中那样）互相通信，那么需要先把它们桥接起来，然后为虚拟网桥设备配置VLAN。



我将 网桥etc0 加入网络后 直接断开了链接

![image-20241015211150816](/笔记/image-20241015211150816.png)

etc 0 是 直接连接到 对应的网卡 上 



现在分配交换机器的原有设置  





## openclash



![image-20241016103745964](/笔记/image-20241016103745964.png)



