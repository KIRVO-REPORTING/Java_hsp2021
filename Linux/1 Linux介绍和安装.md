# Linux

## 介绍

### 1 主要发行版

UBuntu(乌班图)、RedHat(红帽)、CentOS

### 2 Unix和Linux发展

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210206170011241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaTE5ODYyMQ==,size_16,color_FFFFFF,t_70)



## 安装

1. 安装VM(虚拟机) Virtual Machine 15.5

2. 再安装**CentOS 7.6** / CentOS 8.1	 (Linux其中一个开发版Redhat)

​		[下载的是DVD格式安装包 .iso]

​		vm与linux系统关系图：

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528141501069.png" alt="image-20220528141501069" style="zoom:67%;" />

3. 在虚拟软件上部署

   - 一个虚拟机软件VMware 可以有多个虚拟机，一个虚拟机装一个系统。

     如：在虚拟机软件VMware上，有CentOS 7.6，CentOS 8.1， Window 7， MacOS等多个虚拟机

   - 在虚拟机软件上，**创建一个虚拟机**

   - 在该虚拟机上，配置好环境(cpu，内存，网络)，再安装 CentOS7.6（把iso包加载入即可，模拟DVD光盘安装操作系统）



| 全名   | 用户名 | 密码      |
| ------ | ------ | --------- |
| 管理员 | root   | cong.o312 |
| lee    | lee    | cong.o312 |
|        |        |           |



三种网路连接模式：

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528160521345.png" alt="image-20220528160521345" style="zoom:67%;" />



![image-20220528161022182](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528161022182.png)

1. 桥接模式：每个虚拟系统和 主机系统/外部其他主机系统 分配的ip地址网段(192.168.0)一致，但超过了256个后，无法继续分配。
2. NAT模式：主机和其虚拟机生成新的网段，该网段允许他们互相通信。并且虚拟系统可以通过主机代理，与外部其他主机系统通信，但不可逆（外部其他主机系统不可以与它进行通信）
3. 主机模式



## 虚拟机

### 虚拟机克隆

使用VMware软件操作：

​	克隆必须先关闭该虚拟机

​	![image-20220528162403527](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528162403527.png)

链接克隆：并没有新建，只是多个引用

完整克隆：新建新的一模一样的虚拟机



### 虚拟机快照

![image-20220528162745426](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528162745426.png)

使用VMware软件，进行拍照or转到某个快照状态。

![image-20220528163013843](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528163013843.png)



### 虚拟机迁移和删除

虚拟机本质的文件，迁移和删除可以通过 cut/copy来操作。VMware软件的移除，并不会真正删除。



### VMware Tools

VMware Tools是VMware虚拟机软件中自带的一种增强工具。可安装到具体的虚拟机中。

![image-20220528170345579](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528170345579.png)

可与主机(windows)共享文件。

1. 通过VMware软件设置，添加共享路径(可添加多个)

2. CentOS系统中，该文件在 /mnt/hgfs/ 下

实际开发中，我们的**主机 Windows系统 与 Linux 不在同一台设备上**。一般Linux在另一台服务器上，需要通过**远程连接**，**所以共享文件的上传下载需要远程方式**。

