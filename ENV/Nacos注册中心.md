## 一、Nacos是什么

nacos是阿里巴巴最新开源的项目，提供了一组简单易用的特性集，帮助你快速实现动态服务发现、服务配置、服务元数据及流量管理。nacos支持基于DNS和基于RPC的服务发现，动态配置服务（配置中心），动态DNS服务。

eureka是Spring Cloud微服务框架默认的也是推荐的服务注册中心，由Netflix公司与2012年将其开源出来，eureka基于REST服务开发，主要用于实现AWS云的中服务定位，以实现中间层服务器的负载均衡和故障转移，遵循着CAP理论中的A（可用性）P（分区容错性）。

### nacos和eureka的区别主要有以下几个方面：

- CAP理论：eureka只支持AP模式，nacos支持CP和AP两种模式，nacos是根据配置识别CP或AP模式，如果注册nacos的client节点注册时是ephemeral=true即为临时节点，那么nacos集群对这个client节点效果就是AP，反之则是CP，即不是临时节点。
- 连接方式：nacos使用的是netty和服务直接进行连接，属于长连接；eureka是使用定时发送和服务进行联系，属于短连接。
- 服务异常剔除：eureka client在默认情况每隔30s向eureka server发送一次心跳，当eureka server在默认连续90s秒的情况下没有收到心跳，会把eureka client从注册表中剔除，在由eureka server 60秒的清除间隔，把eureka client给下线；nacos client通过心跳上报方式告诉nacos注册中心健康状态，默认心跳间隔5秒，nacos会在超过15秒未收到心跳后将实例设置为不健康状态，可以正常接收到请求；超过30秒nacos将实例删除，不会再接收请求。
- 操作实例方式：nacos提供了nacos console可视化控制话界面，可以对实例列表进行监听，对实例进行上下线，权重的配置，并且config server提供了对服务实例提供配置中心，且可以对配置进行CRUD,版本管理；eureka仅提供了实例列表，实例的状态，错误信息，相比于nacos过于简单。
- 自我保护机制：eureka保护方式：当在短时间内，统计续约失败的比例，如果达到一定阈值，则会触发自我保护的机制，在该机制下，eureka server不会剔除任何的微服务，等到正常后，再退出自我保护机制；nacos保护方式：当域名健康实例（instance）占总服务实例（instance）的比例小于阈值时，无论实例（instance）是否健康，都会将这个实例（instance）返回给客户端。这样做虽然损失了一部分流量，但是保证了集群的剩余健康实例（instance）能正常工作。

#### CP和AP两种模式是什么

CP和AP两种模式是指在分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）三个要素最多只能同时实现两点，不可能三者兼顾的情况。

CP模式是指选择一致性和分区容错性，为了保证数据库的一致性，就必须拒绝请求，否则无法保证一致性。在这个过程中，系统处于不可用状态（失去了A属性）。

AP模式是指选择可用性和分区容错性，当网络分区出现后，为了保证可用性，系统可以返回旧值，保证系统的可用性。但是这样做就违背了一致性C的要求，只满足可用性和分区容错性，即AP。



[使用Docker暗装Nacos](https://blog.csdn.net/weixin_43792363/article/details/130611490)



## 二、服务注册到Nacos

Nacos是SpringCloudAlibaba的组件，而SpringCloudAlibaba也遵循SpringCloud中定义的服务注册、服务发现规范。因此使用Nacos和使用Eureka对于微服务来说，并没有太大区别。

主要差异在于：

- 依赖不同
- 服务地址不同

**本案例有两个服务user-service和order-service**

user-service配置两个实例

![复制实例](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513155533348.png)

![配置端口](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513155611302.png)



### 1、引入依赖

在cloud-demo父工程的pom文件中的`<dependencyManagement>`中引入SpringCloudAlibaba的依赖：

```yaml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

然后在user-service和order-service中的pom文件中引入nacos-discovery依赖：

``` yaml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

### 2、配置nacos地址

在user-service和order-service的application.yml中添加nacos地址：

``` yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
```



### 3、重启微服务

重启微服务后，登录nacos管理页面，可以看到微服务信息：

![nacos服务列表](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513153711311.png)



### 4、**order-service调用user-service**关键代码

#### order-service

**OrderController**

```java
import com.lgl.order.pojo.Order;
import com.lgl.order.pojo.User;
import com.lgl.order.service.OrderService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
@RequestMapping("order")
public class OrderController {

    @Autowired
    private OrderService orderService;

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("{orderId}")
    public Order queryOrderByUserId(@PathVariable("orderId") Long orderId) {
        // 1、根据id查询订单
        Order order = orderService.queryOrderById(orderId);

        // 2、远程查询user
        // 2.1远程地址
        String url = "http://userservice:8081/user/" + order.getUserId();
        // 2.2发起调用
        User user = restTemplate.getForObject(url, User.class);
        // 3.存入order
        order.setUser(user);
        // 4.返回
        return order;
    }
}
```

#### user-service

**UserController**

```java
import com.lgl.user.pojo.User;
import com.lgl.user.service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * 路径： /user/110
     *
     * @param id 用户id
     * @return 用户
     */
    @GetMapping("/{id}")
    public User queryById(@PathVariable("id") Long id) {
        return userService.queryById(id);
    }
}
```

重启微服务，浏览器输入地址`http://localhost:8080/order/101`

![order-service调用user-service](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513154735943.png)



## 三、服务分级存储模型

一个**服务**可以有多个**实例**，例如我们的user-service，可以有:

- 127.0.0.1:8081
- 127.0.0.1:8082
- 127.0.0.1:8083

假如这些实例分布于全国各地的不同机房，例如：

- 127.0.0.1:8081，在上海机房
- 127.0.0.1:8082，在上海机房
- 127.0.0.1:8083，在杭州机房

Nacos就将同一机房内的实例 划分为一个**集群**。

也就是说，user-service是服务，一个服务可以包含多个集群，如杭州、上海，每个集群下可以有多个实例，形成分级模型，如图：

![分级模型](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513155023771.png)

微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。当本集群内不可用时，才访问其它集群。杭州机房内的order-service应该优先访问同机房的user-service。



### 1、给user-service配置集群

修改user-service的application.yml文件，添加集群配置：

``` yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

重启两个user-service实例后，我们可以在nacos控制台看到下面结果：

``` yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

重启两个user-service实例后，我们可以在nacos控制台点击服务详情看到下面结果：

![nacos微服务集群](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513161233152.png)



我们再次复制一个user-service启动配置，添加属性：

``` yaml
-Dserver.port=8085 -Dspring.cloud.nacos.discovery.cluster-name=SH
```



![复制user-service微服务实例](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513161935328.png)

启动UserApplication3后再次查看nacos控制台：



![nacos服务列表](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513162034577.png)

![nacos服务详情集群](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513162114655.png)



### 2、同集群优先的负载均衡

默认的`ZoneAvoidanceRule`并不能实现根据同集群优先来实现负载均衡。

因此Nacos中提供了一个`NacosRule`的实现，可以优先从同集群中挑选实例。

#### 2-1、给order-service配置集群信息

修改order-service的application.yml文件，添加集群配置：

``` yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

#### 2-2、修改负载均衡规则

修改order-service的application.yml文件，修改负载均衡规则：

``` yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则 
```

浏览器调取order-service接口，可以看到user-service3控制台没有调用的日志，因为order-service与user-service3属于不同的集群。

![user-service2控制台](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513163621916.png)

![user-service3控制台](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513163542792.png)



## 四、权重配置

实际部署中会出现这样的场景：

服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求。

但默认情况下NacosRule是同集群内随机挑选，不会考虑机器的性能问题。

因此，Nacos提供了权重配置来控制访问频率，0~1（默认为1）权重越大则访问频率越高。

**在nacos控制台，找到user-service的实例列表，点击编辑，即可修改权重：**

![修改权重](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513164242602.png)

**注意**：如果权重修改为0，则该实例永远不会被访问



## 五、环境隔离

Nacos提供了namespace来实现环境隔离功能。

- nacos中可以有多个namespace
- namespace下可以有group、service等
- 不同namespace之间相互隔离，例如不同namespace的服务互相不可见

### 1、创建namespace

默认情况下，所有service、data、group都在同一个namespace，名为public：

![默认namespace](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513164906933.png)

我们可以点击页面新建命名空间按钮，添加一个namespace：

![新建命名空间](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513165056280.png)

![明名空间列表](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513165132504.png)



### 2、给微服务配置namespace

给微服务配置namespace只能通过修改配置来实现。

例如，修改order-service的application.yml文件：

``` yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ
        namespace: dfe8cbde-0ec5-43b6-861f-aa8afaee1269 # 命名空间，填ID
```

重启order-service后，访问控制台，可以看到下面的结果：

![dev命名空间](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513165552077.png)

此时访问order-service，因为namespace不同，会导致找不到userservice，控制台会报错：

![访问order-service报错](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513165743414.png)



## 六、切换为永久实例

前面说到**Nacos支持AP和CP两种模型，Eureka只支持AP模型**

可以简单理解为

- AP临时实例：如果实例宕机超过一定时间，会从服务列表剔除，默认的类型。
- CP非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。

Nacos默认采用AP模型，我们可以通过配置切换到CP模型

``` yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例
```





## 七、Nacos与Eureka的区别

Nacos和Eureka整体结构类似，服务注册、服务拉取、心跳等待，但是也存在一些差异：

![Nacos模型](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20230513170655288.png)



- Nacos与eureka的共同点
  - 都支持服务注册和服务拉取
  - 都支持服务提供者心跳方式做健康检测

- Nacos与Eureka的区别
  - Nacos支持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式
  - 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
  - Nacos支持服务列表变更的消息推送模式，服务列表更新更及时
  - Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式



