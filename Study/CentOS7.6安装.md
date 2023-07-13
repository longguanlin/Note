## CentOS7.6安装

### 一、CentOS7.6下载

[官网地址](http://vault.centos.org/)

1. 进入CentOS下载官网，找到CentOS7.6版本

2. CentOS7.6 ==>isos==>x86_64 选择torrent文件下载

3. 该文件是个种子，需要将该文件拖到迅雷下载ISO文件

   ![image-20211226211453591](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226211453591.png)

### 二、创建虚拟机CentOS7.6

#### 1、打开VMware,点击“创建新的虚拟机”

#### 2、选择“典型”，点击下一步

   ![image-20211226211910123](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226211910123.png)

#### 3、点击“稍后安装操作系统”

   ![image-20211226212037936](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226212037936.png)

 #### 4、点击Linux，选择安装CentOS 7 64位（我们下载的是CentOs 7.6 64位）

   ![image-20211226212348431](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226212348431.png)

#### 5、更改虚拟机名称和安装位置

   ![image-20211226212801541](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226212801541.png)

#### 6、磁盘选择默认为20.0GB，点选将虚拟磁盘存储为单个文件(O)，并点击“下一步”

#### ![image-20211226213127829](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226213127829.png)

#### 7、点击“完成”

###    三、安装CentOS7.6

#### 1、点击“编辑虚拟机设置”

![image-20211226213619730](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226213619730.png)

#### 2、选择“使用ISO镜像文件”，选择下载好的ISO文件

![image-20211226213936050](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226213936050.png)

####    3、移除USB控制器、声卡、打印机（这一步可以跳过，移除只是为了省内存）

![image-20211226215337110](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226215337110.png)

#### 4、点击“开启此虚拟机”

#### ![image-20211226215441282](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226215441282.png)5、选择Install CentOS 7(ctrl + G进入虚拟机，ctrl + alt 移出虚拟机)

![image-20211226215903631](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226215903631.png)

#### 6、配置语言（默认为英语）、时区（进入DATE & TIME，选择上海)

![image-20211226220925894](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226220925894.png)

#### 7、设置软件选择 (SOFTWARE SELECTION)

![image-20211226221236187](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226221236187.png)

#### 8、如果希望安装有界面的CentOS（选择Server with GUI(带图形用户界面的服务器)，默认为Minimal Install (最小安装)不推荐安装有界面的，平时都可以通过命令行执行，作用不大）

#### 9、设置安装位置 (INSTALLATION DESTINATION)

![image-20211226221610003](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226221610003.png)

##### 9.1、选择 I will configure partitioning

![image-20211226221704240](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226221704240.png)

##### 9.2、更改模式，标准分区Standard Partition，点击“+”按钮添加分区

![image-20211226221838470](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226221838470.png)

##### 9.3、添加 /boot分区，大小300MB，Add mount point

![image-20211226222049750](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222049750.png)

![image-20211226222122120](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222122120.png)

##### 9.4添加 swap分区，一般情况是物理内存的2倍大小，用于物理内存不足时使用，但可能造成系统不稳定，所以看情况，可以设置小一点，甚至设置为0MB，这里我设置为512MB，Add mount point

##### 9.5添加根分区，不填写大小，即默认为剩下内存的全部，点击Done

![image-20211226222449333](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222449333.png)

##### 9.6点击Accept Changes

![image-20211226222543303](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222543303.png)

#### 10、点击Begin Installation 开始安装

![image-20211226222624643](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222624643.png)

#### 11、选择ROOT PASSWORD设置密码

![image-20211226222847414](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226222847414.png)

#### 12、安装完成，点击reboot

![image-20211226223638388](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226223638388.png)

###    三、CentOS7.6基本配置

#### 1、输入账号root密码（上一步设置的密码）

![image-20211226225536268](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20211226225536268.png)

#### 2、配置IP地址，网关

```bash
cd /etc/sysconfig/network-scripts/    //进入到network-scripts目录下  
 
vi ifcfg-ens32  //编辑配置文件 
//按i键进入编辑模式，编辑完成后按Esc键命令模式，按下shift键和:键进入末行模式，再按下w键和q键回车保存退出
 
//修改以下内容
BOOTPROTO=static  //启用静态IP地址
ONBOOT=yes      //开启自动启用网络连接
 
 
//添加以下内容 （想要知道为什么ip地址、子网掩码、网关设置成这个，可以看下面的解释）
IPADDR=192.168.176.124  //设置IP地址
NETMASK=255.255.255.0   //子网掩码
GATEWAY=192.168.176.2   //设置网关
```

![image-20220306230953517](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20220306230953517.png)

#####    问题一：子网掩码和网关为何设置为255.255.255.0和192.168.176.2 

VmWare左上方**编辑**按钮进入**虚拟机网络编辑器**，在安装时默认选择的是NAT模式，所以我们选择VMnet8，再点击**NAT设置**，即可查看VMnet8下的子网IP、子网掩码和网关

![image-20220306232338589](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20220306232338589.png)

可以看到我的VMnet8下的子网掩码为255.255.255.0、网关IP为192.168.176.2，在虚拟机配置文件时，需要与这里的一致。

*ps：如果子网IP和网关IP不在同一个网段下，也就是192.168.176.下，需要将网关IP修改与子网IP一致，最后一位默认即可*

##### 问题二：IP地址设置192.168.176.124

电脑右下角点击网络，选择打开**网络和 Internet**，选择VMnet8网卡，查看IPv4地址。

![image-20220306234117897](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20220306234117897.png)

可以看到我IPv4地址为192.168.176.1，可以发现IPv4地址和子网IP和网关都处于192.168.176网段下*（如果不在同一网段下，请修改VMnet8下的IPv4地址与子网IP和网关处于同一网段下，最后一位默认即可）*   

这也是我虚拟机IP地址设置为192.168.176.124的原因，我们在设置虚拟机ip地址的时候，需要保证虚拟机的ip地址与VMnet8下的IP地址处于同一个网段下，最后一位数不相同即可。*（最后一位不能设置跟子网IP、网关、VMnet8的IPv4地址相同，也就是不能设置为0、2、1）*



   

   

   

   

   

   

   

   

   

   