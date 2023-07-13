## 搭建一个SpringCloud基础项目

### 一、创建主项目dragoncode

1. 创建Maven项目，将src文件夹删除（主项目是一个空的项目，不需要src）

2. File->settings->plugins->marketplace 安装Lombok插件

3. 导入依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <packaging>pom</packaging>
   
       <groupId>org</groupId>
       <artifactId>dragoncode</artifactId>
       <version>1.0-SNAPSHOT</version>
       <name>dragoncode</name>
       <description>Demo project for Spring Boot</description>
   
       <properties>
           <maven.compiler.source>8</maven.compiler.source>
           <maven.compiler.target>8</maven.compiler.target>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
           <mysql.version>5.1.47</mysql.version>
           <druid.version>1.1.10</druid.version>
           <junit.version>4.1.2</junit.version>
           <lombok.version>1.16.10</lombok.version>
           <log4j.vsrsion>1.2.17</log4j.vsrsion>
       </properties>
   
       <dependencyManagement>
           <dependencies>
               <!-- springcloud的依赖-->
               <dependency>
                   <groupId>org.springframework.cloud</groupId>
                   <artifactId>spring-cloud-dependencies</artifactId>
                   <version>Greenwich.SR1</version>
                   <type>pom</type>
                   <scope>import</scope>
               </dependency>
               <!-- springboot的依赖-->
               <dependency>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-dependencies</artifactId>
                   <version>2.1.4.RELEASE</version>
                   <type>pom</type>
                   <scope>import</scope>
               </dependency>
               <!--  数据库-->
               <dependency>
                   <groupId>mysql</groupId>
                   <artifactId>mysql-connector-java</artifactId>
                   <version>${mysql.version}</version>
               </dependency>
               <dependency>
                   <groupId>com.alibaba</groupId>
                   <artifactId>druid</artifactId>
                   <version>${druid.version}</version>
               </dependency>
               <!--单元测试 -->
               <dependency>
                   <groupId>junit</groupId>
                   <artifactId>junit</artifactId>
                   <version>4.12</version>
               </dependency>
               <!-- lombok-->
               <dependency>
                   <groupId>org.projectlombok</groupId>
                   <artifactId>lombok</artifactId>
                   <version>1.18.8</version>
               </dependency>
   
               <!-- log4j-->
               <dependency>
                   <groupId>log4j</groupId>
                   <artifactId>log4j</artifactId>
                   <version>${log4j.vsrsion}</version>
               </dependency>
               <dependency>
                   <groupId>ch.qos.logback</groupId>
                   <artifactId>logback-core</artifactId>
                   <version>1.2.3</version>
               </dependency>
           </dependencies>
       </dependencyManagement>
   
   </project>
   ```

### 二、创建公共子项目cloud-api-commons

1. 导入依赖

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <parent>
           <artifactId>dragoncode</artifactId>
           <groupId>org</groupId>
           <version>1.0-SNAPSHOT</version>
       </parent>
       <modelVersion>4.0.0</modelVersion>
   
       <artifactId>cloud-api-commons</artifactId>
   
       <properties>
           <maven.compiler.source>8</maven.compiler.source>
           <maven.compiler.target>8</maven.compiler.target>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <optional>true</optional>
           </dependency>
           <!-- 工具包-->
           <dependency>
               <groupId>cn.hutool</groupId>
               <artifactId>hutool-all</artifactId>
               <version>5.1.0</version>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-data-jpa</artifactId>
               <version>2.4.2</version>
           </dependency>
       </dependencies>
   </project>
   ```

2. maven -> reroad project重新下载依赖
3. 





