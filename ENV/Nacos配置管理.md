# Nacos配置管理

Nacos配置管理是一种应用于分布式系统的功能，它可以让用户通过Nacos Server控制台集中管理多个服务的配置，每个服务从Nacos Server统一获取自己的配置，并监控配置的变化。Nacos配置管理可以实现以下目标：

- 统一管理微服务的配置，避免配置文件的分散和冗余
- 动态修改和刷新配置，无需重启应用
- 支持多环境、多集群、多命名空间的配置隔离
- 支持配置版本管理和回滚
- 支持配置变更监听和推送



## 1.统一配置管理

当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会让人抓狂，而且很容易出错。我们需要一种统一配置管理方案，可以集中管理所有实例的配置。

![image-20230514190833174](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514190833174.png)

Nacos一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，实现配置的热更新。



### 1.1.在nacos中添加配置文件

![image-20230514191032494](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514191032494.png)

![image-20230514191748806](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514191748806.png)



> 注意：项目的核心配置，需要热更新的配置才有放到nacos管理的必要。基本不会变更的一些配置还是保存在微服务本地比较好。

### 1.2.从微服务拉取配置

微服务要拉取nacos中管理的配置，并且与本地的application.yml配置合并，才能完成项目启动。

但如果尚未读取application.yml，又如何得知nacos地址呢？

因此spring引入了一种新的配置文件：bootstrap.yaml文件，会在application.yml之前被读取，流程如下：

![image-20230514192240829](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514192240829.png)

#### 1.2.1.引入nacos依赖

首先，在user-service服务中，引入nacos-config的客户端依赖：

``` xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

#### 1.2.2.添加bootstrap.yaml

然后，在user-service中添加一个bootstrap.yaml文件，内容如下：

``` yaml
spring:
  application:
    name: userservice # 服务名称
  profiles:
    active: dev #开发环境，这里是dev 
  cloud:
    nacos:
      server-addr: localhost:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
        namespace: dfe8cbde-0ec5-43b6-861f-aa8afaee1269 # 命名空间，填ID(非public命名空间时要填)
```

> 这里会根据spring.cloud.nacos.server-addr获取nacos地址，再根据`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`作为文件id，来读取配置。本例中，就是去读取`userservice-dev.yaml`：

![image-20230514192758967](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514192758967.png)

#### 1.2.3.读取nacos配置

在user-service中的UserController中添加业务逻辑，读取pattern.dateformat配置：

![image-20230514193409705](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514193409705.png)

``` java

@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @Value("${pattern.dateformat}")
    private String dateformat;

    @GetMapping("now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
    }
}

```

#### 1.2.4.重启user-service微服务进行访问

![image-20230514195327144](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514195327144.png)



## 2.配置热更新

我们最终的目的，是修改nacos中的配置后，微服务中无需重启即可让配置生效，也就是**配置热更新**。

要实现配置热更新，可以使用两种方式：

#### 2.1.方式一

在@Value注入的变量所在类上添加注解@RefreshScope：

![image-20230514201744757](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514201744757.png)



#### 2.2.方式二

使用@ConfigurationProperties注解代替@Value注解。

在user-service服务中，添加一个类，读取patterrn.dateformat属性：

``` java
package com.lgl.user.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @Author 15911
 * @Date 2023/5/14 20:12
 * @Version 1.0
 */
@Component
@Data
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {

    private String dateformat;
}
```

在UserController中使用这个类代替@Value：

![image-20230514201929764](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230514201929764.png)



## 3.配置共享

其实微服务启动时，会去nacos读取多个配置文件，例如：

- `[spring.application.name]-[spring.profiles.active].yaml`，例如：userservice-dev.yaml
- `[spring.application.name].yaml`，例如：userservice.yaml

而`[spring.application.name].yaml`不包含环境，因此可以被多个环境共享。

*下面我们通过案例来测试配置共享*



### 3.1.添加一个环境共享配置

我们在nacos中添加一个userservice.yaml文件：

![image-20230515213124385](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515213124385.png)

### 3.2.在user-service中读取共享配置

在user-service服务中，修改PatternProperties类，读取新添加的属性：

![image-20230515205344999](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515205344999.png)

在user-service服务中，修改UserController，添加一个方法：

``` java
	//配置热更新方式二
    @Autowired
    private PatternProperties patternProperties;

    @GetMapping("prop")
    public PatternProperties prop(){
        return patternProperties;
    }
```

### 3.3.运行两个UserApplication，使用不同的profile

修改UserApplication2这个启动项，改变其profile值：

![image-20230515213403920](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515213403920.png)



这样，UserApplication(8081)使用的profile是dev，UserApplication2(8083)使用的profile是test。

启动UserApplication和UserApplication2

访问`http://localhost:8081/user/prop`，结果：

![image-20230515213657886](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515213657886.png)

访问`http://localhost:8083/user/prop`，结果：

![image-20230515213155855](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515213155855.png)

可以看出来，不管是dev，还是test环境，都读取到了envSharedValue这个属性的值。

### 3.4.配置共享的优先级

当nacos、服务本地同时出现相同属性时，优先级有高低之分：

![image-20230515214020841](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230515214020841.png)











