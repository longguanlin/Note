# Maven下载及配置

## 一、Maven下载

[Maven官网下载]([Maven – Download Apache Maven](https://maven.apache.org/download.cgi))

![image-20210913220038829](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913220038829.png)

### 历史版本：

<img src="C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913220407823.png" alt="image-20210913220407823" style="zoom: 80%;" /><img src="C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913220452251.png" alt="image-20210913220452251" style="zoom:67%;" />

<img src="C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913220717162.png" alt="image-20210913220717162" style="zoom:80%;" />



<img src="C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913220909956.png" alt="image-20210913220909956" style="zoom:67%;" />



## 二、解压下载后的文件

![image-20210913221318201](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913221318201.png)

[每个文件所代表的含义](https://www.jb51.net/article/122251.htm)



## 三、配置环境变量

打开我的电脑，右击此电脑，点击属性打开控制面板->高级系统设置->环境变量

### 1、新建系统变量

变量名 **MAVEN_HOME**

![image-20210913222409626](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913222409626.png)

### 2、添加Path路径

<img src="C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913222723354.png" alt="image-20210913222723354" style="zoom:80%;" />

此处还可以写成 **%MAVEN_HOME%\bin**

确认保存后，win+R打开命令提示符，输入cmd打开终端

![image-20210913222928901](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913222928901.png)

输入mvn -v查看版本，出现以下信息则表示配置成功

![image-20210913223138401](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913223138401.png)

## 四、本地仓库及远程仓库配置

进入maven文件的conf文件夹，配置settings.xml文件

![image-20210913231019068](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913231019068.png)

配置本地仓库

![image-20210913230946152](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913230946152.png)

```xml
<localRepository>D:/env/.m2</localRepository>
```

```xml
<!-- 配置阿里云镜像,也可配置其它的国内镜像，用于提升下载速度 -->
<mirrors>
	<mirror> 
      <id>alimaven</id> 
      <name>aliyun maven</name> 
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
      <mirrorOf>central</mirrorOf> 
    </mirror> 
</mirrors>
```





## 五、IDEA配置Maven

从IDEA新窗口中打开：idea->configure->setting

或者从IDEA项目中打开：idea->file->new projects setup ->settings for new projects

![image-20210913225652991](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913225652991.png)

搜索maven，配置参数

![image-20210913232141444](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913232141444.png)

Maven下的Runner—>设置VM options为：

```xml
-DarchetypeCatalog=internal
```

否则会报错 ：

```
No archetype found in remote catalog. Defaulting to internal catalog
```

![image-20210913232543044](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913232543044.png)

至此，Maven安装及IDEA配置完成

## 六、依赖导入

[maven中央仓库](https://mvnrepository.com/)

![image-20210913232928065](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913232928065.png)



## 七、maven常用指令

![image-20210913233634712](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210913233634712.png)





