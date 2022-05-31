## 目录结构

### 总体介绍

Windows系统是：分C/D/E等盘。 

Linux系统的文件系统采用层式的树状目录结构，在此结构最上层的根目录 “/” 。

![image-20220528173939003](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528173939003.png)



在Linux系统里，一切皆文件。（如：cpu，gpu等硬件，在linux是以文件存储的，在device目录下）



### 具体目录介绍

![image-20220528174651982](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528174651982.png)

- loss+found隐藏起来了，可以通过终端查看

![image-20220528174910752](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528174910752.png)

- /proc /srv /sys 这三个目录是系统文件，建议不动。

![image-20220528175155439](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528175155439.png)

![image-20220528175627888](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528175627888.png)





## 远程管理

### 介绍

实际开发中，Linux服务器是开发小组共享的，放到公网上(即所有国家人民都能访问)。

因此，程序员需要进行远程登陆到Linux进行项目管理或开发。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528180334715.png" alt="image-20220528180334715" style="zoom:67%;" />

远程登陆：**xshell技术** （输命令操作等）

远程传输(上传or下载)：**xftp技术**



###  远程登陆

**Xshell6 技术**

![image-20220528180649036](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528180649036.png)

配置步骤：

![image-20220528182636709](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528182636709.png)

- 名称随意填，主机必须填连接服务器的ip地址

​		ifconfig: 查看网络信息(IP地址)

​		ping + IP地址: 检查两台设备网络是否连通（检查当前ip地址与**该ip地址**网络是否连通） 



### 远程传输

Xftp7

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528195504968.png" alt="image-20220528195504968" style="zoom:67%;" />

同样步骤，新建连接。输入ip地址，账号密码。

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528195301995.png" alt="image-20220528195301995" style="zoom:67%;" />

左边为主机，右边为Linux。文件右键即可传输。



## Vi和Vim介绍

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200404747.png" alt="image-20220528200404747" style="zoom:80%;" />

如： vim hello.java



### Vi和Vim常用的三种模式

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200443896.png" alt="image-20220528200443896" style="zoom:80%;" />



### Vi和Vim各个模式的切换

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200540524.png" alt="image-20220528200540524" style="zoom: 80%;" />



### Vi和Vim快捷键

<img src="C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528200731019.png" alt="image-20220528200731019" style="zoom:80%;" />

https://www.bilibili.com/video/BV1Sv411r7vd?p=17&spm_id_from=pageDriver



## 常用指令



#### 关机&重启命令

![image-20220528201053884](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528201053884.png)

shutdown -h （halt）

shutdown -r  （reboot）



#### 用户登录和注销

![image-20220528201734055](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220528201734055.png)

若：在普通用户切到root，输入logout命令，系统会退回到普通用户界面，再次logout退出系统(要在shell执行才有效)。

