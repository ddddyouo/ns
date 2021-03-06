#	基于VirtualBox的网络攻防基础环境搭建

####	一  任务清单
-	[x]	靶机可以直接访问攻击者主机
-	[x]	攻击者主机无法直接访问靶机
-	[x]	网关可以直接访问攻击者主机和靶机
-	[x]	靶机的所有对外上下行流量必须经过网关
-	[x]	所有节点均可以访问互联网
-	[x]	所有节点制作成基础镜像（多重加载的虚拟硬盘）

####	二	实验过程
*	设置多重加载的虚拟硬盘
![微信截图_20170927215942.png](http://upload-images.jianshu.io/upload_images/8142145-f9b324e2a886aa90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![微信截图_20170927220008.png](http://upload-images.jianshu.io/upload_images/8142145-5e0ebe410e1ca7ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![微信截图_20170927220015.png](http://upload-images.jianshu.io/upload_images/8142145-221000e6f5e0721b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*	设置靶机、攻击者主机、网关的网卡
	*	靶机、攻击者主机、网管之间的网络使用Host-Only网卡
	*	连接外网使用NAT网络
	*	Host-Only网络设置
![image002.jpg](http://upload-images.jianshu.io/upload_images/8142145-444d7fa4541a6ab8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	靶机只设一个网卡，连接方式选择Host-Only
![image008.jpg](http://upload-images.jianshu.io/upload_images/8142145-d715058fd42667d2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	攻击者主机同靶机设置一样
![image006.jpg](http://upload-images.jianshu.io/upload_images/8142145-b4cbe57ee3befb3f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	网关设置两个网卡，一个选择Host-Only，一个选择NAT网络
![image004.jpg](http://upload-images.jianshu.io/upload_images/8142145-191ebe8c62a02b17.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*	设置靶机、攻击者、网关主机的IP
	*	网关使用默认分配的IP
![image013.jpg](http://upload-images.jianshu.io/upload_images/8142145-f17aa8d62ee1408a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	设置靶机IP（192.168.56.2），Gateway填写网关IP（192.168.56.101）
![image015.jpg](http://upload-images.jianshu.io/upload_images/8142145-5a217e0a9bf7ea96.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	设置攻击者主机IP（192.168.56.3），Gateway填写网关IP（192.168.56.101）
![image017.jpg](http://upload-images.jianshu.io/upload_images/8142145-b656073b3659ea4d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*	禁止攻击者主机直接访问靶机
	*	在靶机终端设置防火墙规则，丢掉来自攻击者主机IP的包
	```shell 
	iptables -I INPUT -s 192.168.26.5 -j DROP
	```
	*	测试靶机ping攻击者，通过监听靶机和攻击者主机可以看到，ping包可以到达攻击者主机，但是reply包却不能到达靶机
![image021.jpg](http://upload-images.jianshu.io/upload_images/8142145-323e587123aa6c38.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image023.jpg](http://upload-images.jianshu.io/upload_images/8142145-95a61e7d96c03838.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	测试网关ping靶机和攻击者主机，均可ping通
![image025.jpg](http://upload-images.jianshu.io/upload_images/8142145-f86356ca5aa5853f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image027.jpg](http://upload-images.jianshu.io/upload_images/8142145-a0596c38de9e8253.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*	设置NAT
	```shell
	iptables -t nat -A POSTROUTING -o eth1 -s 192.168.26.0/24 -j MASQUERADE
	```
	*	测试靶机访问外网IP并监听流量是否经过网关，成功
![image029.jpg](http://upload-images.jianshu.io/upload_images/8142145-5fdf9bc5a66645fe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	*	测试攻击者主机访问外网IP并监听流量是否经过网关，成功
![image031.jpg](http://upload-images.jianshu.io/upload_images/8142145-38d66bdac08f62a7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####	（图片因为我调了尺寸变得很糊，不知道怎么解决。不是网上找的图片！）