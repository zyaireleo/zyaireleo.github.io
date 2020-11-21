---
title: AlibabaCloud-docker
date: 2020-11-10 19:30:12
tags: AlibabaCloud

---

### 引言

#### 微服务架构常见核心组件

- 网关
  - 路由+过滤器
- 服务注册发现
  - 调用和被调用方的信息维护
- 配置中心
  - 管理配置，动态更新 application.properties
- 链路追踪
  - 分析调用链路耗时，保存数据库
- 负载均衡器
  - 分发流量到多个节点，降低压力
- 熔断
  - 保护自己和被调用方

#### 微服务架构常见解决方案

- ServiceComb
  - 华为内部的CSE(Cloud Service Engine)框架开源, 一个微服务的开源解决方案,社区相对于下面几个比较小
  - 文档不多，通信领域比较强
- dubbo
  - zookeeper + dubbo + springmvc/springboot
  - 官方地址：http://dubbo.apache.org/#!/?lang=zh-cn
  - 配套
    - 通信方式：rpc
    - 注册中心：zookeper/redis/nacos
    - 配置中心：diamond、nacos
- SpringCloud
  - 全家桶+轻松嵌入第三方组件(Netflix 奈飞)
  - 官网：https://spring.io/projects/spring-cloud
  - 配套
    - 通信方式：http restful
    - 注册中心：eruka
    - 配置中心：config
    - 断路器：hystrix
    - 网关：zuul/gateway
    - 分布式追踪系统：sleuth+zipkin
- Spring Alibaba Cloud
  - 全家桶+阿里生态多个组件组合+SpringCloud支持
  - 官网 https://spring.io/projects/spring-cloud-alibaba
  - 配套
    - 通信方式：http restful
    - 注册中心：nacos
    - 配置中心：nacos
    - 断路器：sentinel
    - 网关：gateway
    - 分布式追踪系统：sleuth+zipkin

#### 微服务核心组件图

![image-20201110200336527](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201110200336527.png)

### 起步

- 创建聚合工程

  ```
  modelVersion>4.0.0</modelVersion>
      <groupId>net.company</groupId>
      <artifactId>cloud-dmeo</artifactId>
      <version>1.0-SNAPSHOT</version>
      <modules>
          <module>module-common</module>
          <module>module1</module>
          <module>module2</module>
          <module>module3r</module>
      </modules>
  
      <!-- 一般来说父级项目的packaging都为pom，packaging默认类型jar类型-->
      <packaging>pom</packaging>
  
      <properties>
          <java.version>1.8</java.version>
          <maven.compiler.source>1.8</maven.compiler.source>
          <maven.compiler.target>1.8</maven.compiler.target>
       </properties>
  ```

- 创建子项目

  ```
   <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
  
          <dependency>
              <groupId>net.company</groupId>
              <artifactId>module-common</artifactId>
              <version>1.0-SNAPSHOT</version>
          </dependency>
      </dependencies>
  ```

- 配置数据库连接（注意 端口、应用名称、数据库名称）

  ```
  server:
    port: 9000
  
  spring:
    application:
      name: module1
    datasource:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://127.0.0.1:3306/cloud_video?useUnicode=true&characterEncoding=utf-8&useSSL=false
      username: root
      password: 123456
  
  # 控制台输出sql、下划线转驼峰
  mybatis:
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
      map-underscore-to-camel-case: true
  ```

### 起飞

#### Think  about it

- 服务间的调用

  ```
  RPC:
    远程过程调用，像调用本地服务(方法)一样调用服务器的服务
    支持同步、异步调用
    客户端和服务器之间建立TCP连接，可以一次建立一个，也可以多个调用复用一次链接
    RPC数据包小
      protobuf
      thrift
    rpc：编解码，序列化，链接，丢包，协议
    
  Rest(Http):
    http请求，支持多种协议和功能
    开发方便成本低
    http数据包大
    java开发：resttemplate或者httpclient
  ```

- 用户间的调用

  ```
  @Bean
  public RestTemplate getRestTemplate(){
      return new RestTemplate();
  }
      
  Video video = restTemplate.getForObject("http://localhost:9000/api/v1/video/find_by_id?videoId="+videoId,Video.class);
  ```

- 存在的问题

  - 服务之间的IP信息写死
  - 服务之间无法提供负载均衡
  - 多个服务直接关系调用维护复杂

#### nacos注册中心

##### 注册中心和常见的注册中心

- 注册中心（服务治理）

  - 服务注册：服务提供者provider，启动的时候向注册中心上报自己的网络信息
    - 服务发现：服务消费者consumer,启动的时候向注册中心上报自己的网络信息，拉取provider的相关网络信息
  - 核心:服务管理,是有个服务注册表，心跳机制动态维护，服务实例在启动时注册到服务注册表，并在关闭时注销。

- 有什么用

  - 微服务应用和机器越来越多，调用方需要知道接口的网络地址，如果靠配置文件的方式去控制网络地址，对于动态新增机器，维护带来很大问题

- 主流的注册中心：zookeeper、Eureka、consul、etcd、Nacos

  |                 | **Nacos**                  | **Eureka** | **Consul**        | **Zookeeper** |
  | :-------------- | :------------------------- | :--------- | :---------------- | :------------ |
  | 一致性协议      | CP+AP                      | AP         | CP                | CP            |
  | 健康检查        | TCP/HTTP/MYSQL/Client Beat | 心跳       | TCP/HTTP/gRPC/Cmd | Keep Alive    |
  | 雪崩保护        | 有                         | 有         | 无                | 无            |
  | 访问协议        | HTTP/DNS                   | HTTP       | HTTP/DNS          | TCP           |
  | SpringCloud集成 | 支持                       | 支持       | 支持              | 支持          |

  - Zookeeper：CP设计，保证了一致性，集群搭建的时候，某个节点失效，则会进行选举行的leader，或者半数以上节点不可用，则无法提供服务，因此可用性没法满足
  - Eureka：AP原则，无主从节点，一个节点挂了，自动切换其他节点可以使用，去中心化

   

  - 结论：
    - 分布式系统中P,肯定要满足，所以只能在CA中二选一
    - 没有最好的选择，最好的选择是根据业务场景来进行架构设计
    - 如果要求一致性，则选择zookeeper/Nacos，如金融行业 CP
    - 如果要求可用性，则Eureka/Nacos，如电商系统 AP
    - CP ： 适合支付、交易类，要求数据强一致性，宁可业务不可用，也不能出现脏数据
    - AP: 互联网业务，比如信息流架构，不要求数据强一致，更想要服务可用

![image-20201110200816781](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201110200816781.png)

##### CAP理论

- CAP定理: 指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），三者不可同时获得

  - 一致性（C）：所有节点都可以访问到最新的数据
  - 可用性（A）：每个请求都是可以得到响应的，不管请求是成功还是失败
  - 分区容错性（P）：除了全部整体网络故障，其他故障都不能导致整个系统不可用

   

- CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。而由于当前的网络硬件肯定会出现延迟丢包等问题，所以分区容忍性是我们必须需要实现的。所以我们只能在一致性和可用性之间进行权衡

  ![image-20201110203816385](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201110203816385.png)

```
CA： 如果不要求P（不允许分区），则C（强一致性）和A（可用性）是可以保证的。但放弃P的同时也就意味着放弃了系统的扩展性，也就是分布式节点受限，没办法部署子节点，这是违背分布式系统设计的初衷的

CP: 如果不要求A（可用），每个请求都需要在服务器之间保持强一致，而P（分区）会导致同步时间无限延长(也就是等待数据同步完才能正常访问服务)，一旦发生网络故障或者消息丢失等情况，就要牺牲用户的体验，等待所有数据全部一致了之后再让用户访问系统

AP：要高可用并允许分区，则需放弃一致性。一旦分区发生，节点之间可能会失去联系，为了高可用，每个节点只能用本地数据提供服务，而这样会导致全局数据的不一致性。
```

##### CAP的权衡结果 BASE理论

- 什么是Base理论

```
CAP 中的一致性和可用性进行一个权衡的结果，核心思想就是：我们无法做到强一致，但每个应用都可以根据自身的业务特点，采用适当的方式来使系统达到最终一致性, 来自 ebay 的架构师提出
```

- Basically Available(基本可用)
  - 假设系统，出现了不可预知的故障，但还是能用, 可能会有性能或者功能上的影响
- Soft state（软状态）
  - 允许系统中的数据存在中间状态，并认为该状态不影响系统的整体可用性，即允许系统在多个不同节点的数据副本存在数据延时 
- Eventually consistent（最终一致性）
  - 系统能够保证在没有其他新的更新操作的情况下，数据最终一定能够达到一致的状态，因此所有客户端对系统的数据访问最终都能够获取到最新的值

##### Nacos搭建

- 下载安装

  官网：https://nacos.io/zh-cn/

- 进入bin目录

- 启动 sh startup.sh -m standalone

- 访问 localhost:8848/nacos

- 默认账号密码 nacos/nacos

##### 项目集成Nacos

- 添加依赖

  ```
  <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
  </dependency>
  // 所有子模块都需要添加
  ```

- 配置Nacos地址

  ```
  server:
    port: 9000
  
  spring:
    application:
      name: servicename
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  ```

- 启动类增加注解

  ```
  @EnableDiscoveryClient
  ```

- 服务之间的调用

  ```
    @Autowired
      private DiscoveryClient discoveryClient;
  
    @Autowired
      private RestTemplate restTemplate;
      
     @RequestMapping("save")
      public VideoOrder save(int videoId){
  
          VideoOrder videoOrder = new VideoOrder();
          videoOrder.setVideoId(videoId);
  
          List<ServiceInstance> list = discoveryClient.getInstances("xdclass-video-service");
  
          ServiceInstance serviceInstance = list.get(0);
  		//订单调视频
          Video video = restTemplate.getForObject("http://"+serviceInstance.getHost()+":"+serviceInstance.getPort()+
                  "/api/v1/video/find_by_id?videoId="+videoId,Video.class);
  
          videoOrder.setVideoTitle(video.getTitle());
          videoOrder.setVideoId(video.getId());
          return videoOrder;
  
      }
  ```

#### Ribbon+Feign实现负载均衡

##### 负载均衡和常见的解决方案

- 什么是负载均衡（Load Balance）
  - 分布式系统中一个非常重要的概念，当访问的服务具有多个实例时，需要根据某种“均衡”的策略决定请求发往哪个节点，这就是所谓的负载均衡，原理是将数据流量分摊到多个服务器执行，减轻每台服务器的压力，从而提高了数据的吞吐量。

- 负载均衡的种类
  - 软硬件角度
    - 通过硬件来进行解决，常见的硬件有NetScaler、F5、Radware和Array等商用的负载均衡器，但比较昂贵的
    - 通过软件来进行解决，常见的软件有LVS、Nginx等,它们是基于Linux系统并且开源的负载均衡策略
  - 端的角度
    - 服务端负载均衡
    - 客户端负载均衡

![image-20201110201930966](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201110201930966.png)

- 常见的负载均衡策略（看组件的支持情况）
  - 节点轮询
    - 简介：每个请求按顺序分配到不同的后端服务器
  - weight 权重配置
    - 简介：weight和访问比率成正比，数字越大，分配得到的流量越高
  - 固定分发
    - 简介：根据请求按访问ip的hash结果分配，这样每个用户就可以固定访问一个后端服务器
  - 随机选择、最短响应时间等等

##### AlibabaCloud集成Ribbon实现负载均衡

- 订单服务增加@LoadBalanced 注解

```
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
  return new RestTemplate();
}
```

- 调用实战

```
Video video = restTemplate.getForObject("http://xdclass-video-service/api/v1/video/find_by_id?videoId="+videoId, Video.class);

注意：方便大家看到负载均衡效果，在video类增加这个字段，记录当前机器ip+端口
```

- 源码解析

  @LoadBalanced 1）首先从注册中心获取provider的列表 2）通过一定的策略选择其中一个节点 3）再返回给restTemplate调用

  其实采用的是轮询策略

- 自定义Ribbon负载均衡策略

  - Ribbon支持的负载均衡策略

  | RandomRule                | 随机策略           | 随机选择server                                               |
  | ------------------------- | ------------------ | :----------------------------------------------------------- |
  | RoundRobinRule            | 轮询策略           | 按照顺序选择server（默认）                                   |
  | RetryRule                 | 重试策略           | 当选择server不成功，短期内尝试选择一个可用的server           |
  |                           |                    |                                                              |
  | AvailabilityFilteringRule | 可用过滤策略       | 过滤掉一直失败并被标记为circuit tripped的server，过滤掉那些高并发链接的server（active connections超过配置的阈值） |
  | WeightedResponseTimeRule  | 响应时间加权重策略 | 根据server的响应时间分配权重，以响应时间作为权重，响应时间越短的服务器被选中的概率越大，综合了各种因素，比如：网络，磁盘，io等，都直接影响响应时间 |
  | ZoneAvoidanceRule         | 区域权重策略       | 综合判断server所在区域的性能，和server的可用性，轮询选择server |

  - 负载均衡策略调整实战

```
订单服务增加配置

xdclass-video-service:
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
 //策略选择： 1、如果每个机器配置一样，则建议不修改策略 (推荐) 2、如果部分机器配置强，则可以改为 WeightedResponseTimeRule
```

Open-Feign

##### Feign

- 什么是Feign:

  ```
  SpringCloud提供的伪http客户端(本质还是用http)，封装了Http调用流程，更适合面向接口化
  让用Java接口注解的方式调用Http请求.
  
  不用像Ribbon中通过封装HTTP请求报文的方式调用 Feign默认集成了Ribbon
  Nacos支持Feign,可以直接集成实现负载均衡的效果
  ```

##### 集成Feign

- 加入依赖

  ```
  <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-openfeign</artifactId>
  </dependency>
  ```

- 配置注解

  ```
  启动类增加@EnableFeignClients
  ```

- 增加一个接口

  ```
  订单服务增加接口，服务名称记得和nacos保持一样
  @FeignClient(name="xdclass-video-service") 
  ```

#### 流控防卫兵Sentinel

##### 高并发下的微服务容错方案

- 限流

- 漏斗，不管流量多大，均匀的流入容器，令牌桶算法，漏桶算法

  ![image-20200908182543365](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200908182543365.png)

- 熔断：

  - 保险丝，熔断服务，为了防止整个系统故障，包含当前和下游服务 下单服务 -》商品服务-》用户服务 -》（出现异常-》熔断风控服务

- 降级：

  - 抛弃一些非核心的接口和数据，返回兜底数据 旅行箱的例子：只带核心的物品，抛弃非核心的，等有条件的时候再去携带这些物品

- 隔离：

- 服务和资源互相隔离，比如网络资源，机器资源，线程资源等，不会因为某个服务的资源不足而抢占其他服务的资源

- 熔断和降级互相交集

  - 相同点：
    - 从可用性和可靠性触发，为了防止系统崩溃
    - 最终让用户体验到的是某些功能暂时不能用
  - 不同点
    - 服务熔断一般是下游服务故障导致的，而服务降级一般是从整体系统负荷考虑，由调用方控制

- 官网：https://github.com/alibaba/Sentinel/wiki/%E4%BB%8B%E7%BB%8D

##### Sentinel控制台搭建

- 引入Sentinel依赖

  ```
  <dependency>
              <groupId>com.alibaba.cloud</groupId>
              <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
  </dependency>
  ```

  注意：Sentinel 控制台目前仅支持单机部

- 启动

  ```
  //启动 Sentinel 控制台需要 JDK 版本为 1.8 及以上版本，
  //-Dserver.port=8080 用于指定 Sentinel 控制台端口为 8080 
  //默认用户名和密码都是 sentinel
  
  java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.0.jar
  ```

- 项目整合Sentinel

  - 接入sentinel

  ```
  spring:
    cloud:
      sentinel:
        transport:
          dashboard: 127.0.0.1:8080 
          port: 9999 
  
  #dashboard: 8080 控制台端口
  #port: 9999 本地启的端口，随机选个不能被占用的，与dashboard进行数据交互，会在应用对应的机器上启动一个 Http Server，该 Server 会与 Sentinel 控制台做交互, 若被占用,则开始+1一次扫描
  ```

  微服务注册上去后，由于Sentinel是懒加载模式，所以需要访问微服务后才会在控制台出现

##### 流量控制规则

- 基于统计并发线程数的流量控制

  ```
  并发数控制用于保护业务线程池不被慢调用耗尽
  
  Sentinel 并发控制不负责创建和管理线程池，而是简单统计当前请求上下文的线程数目（正在执行的调用数目）
  
  如果超出阈值，新的请求会被立即拒绝，效果类似于信号量隔离。
  ```

- 基于统计QPS的流量控制

  ```
  当 QPS 超过某个阈值的时候，则采取措施进行流量控制
  ```

###### 基于并发线程数限制流量控制

```
并发数控制用于保护业务线程池不被慢调用耗尽

Sentinel 并发控制不负责创建和管理线程池，而是简单统计当前请求上下文的线程数目（正在执行的调用数目）

如果超出阈值，新的请求会被立即拒绝，效果类似于信号量隔离。并发数控制通常在调用端进行配
```

- 效果

  - 直接拒绝：默认的流量控制方式，当QPS超过任意规则的阈值后，新的请求就会被立即拒绝

  - Warm Up：冷启动/预热，如果系统在此之前长期处于空闲的状态，我们希望处理请求的数量是缓步的增多，经过预期的时间以后，到达系统处理请求个数的最大值

    ![image-20200908212417778](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200908212417778.png)

  - 匀速排队：严格控制请求通过的间隔时间，也即是让请求以均匀的速度通过，对应的是漏桶算法，主要用于处理间隔性突发的流量，如消息队列，想象一下这样的场景，在某一秒有大量的请求到来，而接下来的几秒则处于空闲状态，我们希望系统能够在接下来的空闲期间逐渐处理这些请求，而不是在第一秒直接拒绝多余的请求

  ![image](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/68292442-d4af3c00-00c6-11ea-8251-d0977366d9b4.png)

- 注意：

  - 匀速排队等待策略是 Leaky Bucket 算法结合虚拟队列等待机制实现的。
  - 匀速排队模式暂时不支持 QPS > 1000 的场景

##### Sentinel熔断降级规则

- 熔断降级（虽然是两个概念，基本都是互相配合）

  - 对调用链路中不稳定的资源进行熔断降级也是保障高可用的重要措施之一
  - 对不稳定的**弱依赖服务调用**进行熔断降级，暂时切断不稳定调用，避免局部不稳定因素导致整体的雪崩
  - 熔断降级作为保护自身的手段，通常在客户端（调用端）进行配置

- Sentinel 熔断策略

  - 慢调用比例(响应时间): 选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用

    - 比例阈值
    - 熔断时长：超过时间后会尝试恢复
    - 最小请求数：熔断触发的最小请求数，请求数小于该值时即使异常比率超出阈值也不会熔断

    ![image-20200909121342893](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200909121342893.png)

  - 异常比例：当单位统计时长内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断

    - 比例阈值
    - 熔断时长：超过时间后会尝试恢复
    - 最小请求数：熔断触发的最小请求数，请求数小于该值时，即使异常比率超出阈值也不会熔断

    ![image-20200909121357918](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200909121357918.png)

  - 异常数：当单位统计时长内的异常数目超过阈值之后会自动进行熔断

    - 异常数:
    - 熔断时长：超过时间后会尝试恢复
    - 最小请求数：熔断触发的最小请求数，请求数小于该值时即使异常比率超出阈值也不会熔断

    ![image-20200909121415806](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200909121415806.png)

#####  Sentinel的熔断状态和恢复

- 服务熔断一般有三种状态

  - 熔断关闭（Closed）

    - 服务没有故障时，熔断器所处的状态，对调用方的调用不做任何限制

  - 熔断开启（Open）

    - 后续对该服务接口的调用不再经过网络，直接执行本地的fallback方法

  - 半熔断（Half-Open）

    - 所谓半熔断就是尝试恢复服务调用，允许有限的流量调用该服务，并监控调用成功率

    ![image-20200909171947975](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20200909171947975.png)

- 熔断恢复：

  - 经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态）尝试恢复服务调用，允许有限的流量调用该服务，并监控调用成功率。
  - 如果成功率达到预期，则说明服务已恢复，进入熔断关闭状态；如果成功率仍旧很低，则重新进入熔断状态

##### Sentinel自定义异常-整合Open-Feign

v2.1.0到v2.2.0后，Sentinel里面依赖进行了改动，且不向下兼容

###### 自定义降级返回数据

- 【旧版】实现UrlBlockHandler并且重写blocked方法

```
@Component
public class XdclassUrlBlockHandler implements UrlBlockHandler {
    @Override
    public void blocked(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, BlockException e) throws IOException {
       //降级业务处理
    }
}
```

- 【新版】实现BlockExceptionHandler并且重写handle方法

```
@Component
public class XdclassUrlBlockHandler implements BlockExceptionHandler {
    @Override
    public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, BlockException e) throws IOException {
        Map<String,Object> backMap=new HashMap<>();
        if (e instanceof FlowException){
            backMap.put("code",-1);
            backMap.put("msg","限流-异常啦");
        }else if (e instanceof DegradeException){
            backMap.put("code",-2);
            backMap.put("msg","降级-异常啦");
        }else if (e instanceof ParamFlowException){
            backMap.put("code",-3);
            backMap.put("msg","热点-异常啦");
        }else if (e instanceof SystemBlockException){
            backMap.put("code",-4);
            backMap.put("msg","系统规则-异常啦");
        }else if (e instanceof AuthorityException){
            backMap.put("code",-5);
            backMap.put("msg","认证-异常啦");
        }

        // 设置返回json数据
        httpServletResponse.setStatus(200);
        httpServletResponse.setHeader("content-Type","application/json;charset=UTF-8");
        httpServletResponse.getWriter().write(JSON.toJSONString(backMap));
    }
}
```

###### Feign整合Sentinel配置

- 整合步骤

  - 加入依赖

  ```
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
  </dependency>
  ```

  - 开启Feign对Sentinel的支持

  ```
  feign:
    sentinel:
      enabled: true
  ```

  - 创建容错类, 实现对应的服务接口, 记得加注解 @Service

  ```
  @Service
  public class VideoServiceFallback implements VideoService {
      @Override
      public Video findById(int videoId) {
          Video video = new Video();
          video.setTitle("熔断降级数据");
          return video;
      }
  
      @Override
      public Video saveVideo(Video video) {
          return null;
      }
  }
  
  ```

  - 配置feign容错类

  ```
  @FeignClient(value = "xdclass-video-service", fallback = VideoServiceFallback.class)
  ```

#### 网关

##### 基础知识

- 网关
  - API Gateway，是系统的唯一对外的入口，介于客户端和服务器端之间的中间层，处理非业务功能 提供路由请求、鉴权、监控、缓存、限流等功能
  - 统一接入
    - 智能路由
    - AB测试、灰度测试
    - 负载均衡、容灾处理
    - 日志埋点（类似Nignx日志）
  - 流量监控
    - 限流处理
    - 服务降级
  - 安全防护
    - 鉴权处理
    - 监控
    - 机器网络隔离
- 主流的网关
  - zuul：是Netflix开源的微服务网关，和Eureka,Ribbon,Hystrix等组件配合使用，依赖组件比较多，性能教差
  - kong: 由Mashape公司开源的，基于Nginx的API gateway
  - nginx+lua：是一个高性能的HTTP和反向代理服务器,lua是脚本语言，让Nginx执行Lua脚本，并且高并发、非阻塞的处理各种请求
  - springcloud gateway: Spring公司专门开发的网关，替代zuul

![image-20201116185905741](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201116185905741.png)

##### 配置

- 加入依赖

  ```
   <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-gateway</artifactId>
  </dependency>
  ```

- 配置

  ```
  server:
    port: 8888
  spring:
    application:
      name: api-gateway
    cloud:
      gateway:
        routes: #数组形式
          - id: order-service  #路由唯一标识
            uri: http://127.0.0.1:8000  #想要转发到的地址
            order: 1 #优先级，数字越小优先级越高
            predicates: #断言 配置哪个路径才转发
              - Path=/order-server/**
            filters: #过滤器，请求在传递过程中通过过滤器修改
              - StripPrefix=1  #去掉第一层前缀
  
  #访问路径 http://localhost:8888/order-server/api/v1/video_order/list
  #转发路径 http://localhost:8000/order-server/api/v1/video_order/list  
  #需要过滤器去掉前面第一层
  ```

##### gateway整合nacos

- 原先存在的问题

  - 微服务地址写死
  - 负载均衡没做到

- 添加Nacos服务治理配置

  - 网关添加naocs依赖

  ```
          <!--添加nacos客户端-->
          <dependency>
              <groupId>com.alibaba.cloud</groupId>
              <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
          </dependency>
  ```

  - 启动类开启支持

  ```
  @EnableDiscoveryClient
  ```

  - 修改配置文件

  ```
  server:
    port: 8888
  spring:
    application:
      name: api-gateway
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  
      gateway:
        routes: #数组形式
          - id: order-service  #路由唯一标识
            #uri: http://127.0.0.1:8000  #想要转发到的地址
            uri: lb://xdclass-order-service  # 从nacos获取名称转发,lb是负载均衡轮训策略
  
            predicates: #断言 配置哪个路径才转发
              - Path=/order-server/**
            filters: #过滤器，请求在传递过程中通过过滤器修改
              - StripPrefix=1 #去掉第一层前缀
        discovery:
          locator:
            enabled: true  #开启网关拉取nacos的服务
  
  
  ## 访问路径 http://localhost:8888/order-server/api/v1/video_order/list
  ```

##### 断言+过滤

业务流程

![image-20201116191730944](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/image-20201116191730944.png)

- 需求：接口需要在指定时间进行下线，过后不可以在被访问
  - 使用Before ,只要当前时间小于设定时间，路由才会匹配请求
  - 东8区的2020-09-11T01:01:01.000+08:00后，请求不可访问
  - 为了方便测试，修改时间即可

```
predicates:
  - Before=2020-09-09T01:01:01.000+08:00
```

- 过滤器

  - 局部过滤器GatewayFilter：应用在某个路由上,每个过滤器工厂都对应一个实现类，并且这些类的名称必须以 GatewayFilterFactory 结尾
  - 全局过滤器：作用全部路由上,

- 自定义全局过滤器实现鉴权

  ```
  @Component
  public class UserGlobalFilter implements GlobalFilter,Ordered {
      @Override
      public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
  
          String token = exchange.getRequest().getHeaders().getFirst("token");
  
          System.out.println(token);
          if(StringUtils.isBlank(token)){
              exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
              return exchange.getResponse().setComplete();
          }
  
          //继续往下执行
          return chain.filter(exchange);
  
      }
  
      //数字越小，优先级越高
      @Override
      public int getOrder() {
          return 0;
      }
  }
  
  ```

- 注意：网关不要加太多业务逻辑，否则会影响性能，务必记住

####  链路追踪

##### 追踪组件Sleuth

- 什么是Sleuth
  - 一个组件，专门用于记录链路数据的开源组件
  - 文档：https://spring.io/projects/spring-cloud-sleuth
  - 案例

```
    [order-service,96f95a0dd81fe3ab,852ef4cfcdecabf3,false]
    
    第一个值，spring.application.name的值
    
    第二个值，96f95a0dd81fe3ab ，sleuth生成的一个ID，叫Trace ID，用来标识一条请求链路，一条请求链路中包含一个Trace ID，多个Span ID
    
    第三个值，852ef4cfcdecabf3、spanid 基本的工作单元，获取元数据，如发送一个http
    
    第四个值：false，是否要将该信息输出到zipkin服务中来收集和展示。
```



- 各个微服务添加依赖

```
<dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

##### 链路追踪系统Zipkin

- 什么是zipkin

  - 官网
    - https://zipkin.io/
    - https://zipkin.io/pages/quickstart.html
  - 大规模分布式系统的APM工具（Application Performance Management）,基于Google Dapper的基础实现，和sleuth结合可以提供可视化web界面分析调用链路耗时情况

- 同类产品

  - 鹰眼（EagleEye）
  - CAT
  - twitter开源zipkin，结合sleuth
  - Pinpoint，运用JavaAgent字节码增强技术

- StackDriver Trace (Google)

  

- 开始使用

  - 安装包在资料里面，启动服务

  ```
  java -jar zipkin-server-2.12.9-exec.jar
  ```

  - 访问入口：http://127.0.0.1:9411/zipkin/
  - zipkin组成：Collector、Storage、Restful API、Web UI组成

![architecture-1](C:/Users/tarena/AppData/Roaming/Typora/draftsRecover/AlibabaCloud-docker/architecture-1.png)

##### Zipkin+Sleuth整合

- sleuth收集跟踪信息通过http请求发送给zipkin server
- zipkin server进行跟踪信息的存储以及提供Rest API即可
- Zipkin UI调用其API接口进行数据展示默认存储是内存，可也用mysql 或者elasticsearch等存储
- 微服务加入依赖

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

- 配置地址和采样百分比配置

```
spring:
  application:
    name: api-gateway
  zipkin:
    base-url: http://127.0.0.1:9411/ #zipkin地址
    discovery-client-enabled: false  #不用开启服务发现

  sleuth:
    sampler:
      probability: 1.0 #采样百分比
默认为0.1，即10%，这里配置1，是记录全部的sleuth信息，是为了收集到更多的数据（仅供测试用）。
在分布式系统中，过于频繁的采样会影响系统性能，所以这里配置需要采用一个合适的值。
```

##### 链路追踪日志持久化

- 现存在的问题

  - 服务重启会导致链路追踪系统数据丢失

- 持久化配置：mysql或者elasticsearch

  - 创建数据库表SQL脚本
  - 启动命令

  ```
  java -jar zipkin-server-2.12.9-exec.jar --STORAGE_TYPE=mysql --MYSQL_HOST=127.0.0.1 --MYSQL_TCP_PORT=3306 --MYSQL_DB=zipkin_log --MYSQL_USER=root --MYSQL_PASS=xdclass.net
  ```

#### nacos配置中心

##### 基础知识

- 配置中心：
  - 一句话：统一管理配置, 快速切换各个环境的配置
- 相关产品：
  - 百度的disconf 地址:https://github.com/knightliao/disconf
  - 阿里的diamand 地址：https://github.com/takeseem/diamond
  - springcloud的configs-server: 地址：http://cloud.spring.io/spring-cloud-config/
  - 阿里的Nacos:既可以当服务治理，又可以当配置中心，Nacos = Eureka + Config

##### 配置

- 项目添加依赖

```
 <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

- 配置文件优先级讲解（坑）

  - 不能使用原先的application.yml, 需要使用bootstrap.yml作为配置文件
  - 配置读取优先级 bootstrap.yml > application.yml

- 配置实操

  - 订单服务迁移配置
  - 增加bootstrap.yml

  ```
  spring:
    application:
      name: xdclass-order-service
    cloud:
      nacos:
        config:
          server-addr: 127.0.0.1:8848 #Nacos配置中心地址
          file-extension: yaml #文件拓展格式
  
    profiles:
      active: dev
  
  ```

- 启动微服务服务验证

  - 测试是否可以获取配置

### 整合docker

#### 配置

- 父项目添加springboot版本依赖

```
 <properties>
        <java.version>11</java.version>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <spring.boot.version>2.3.3.RELEASE</spring.boot.version>
  </properties>
```

- 每个子模块项目添加依赖

```
//配置文件增加
<docker.image.prefix>PREFIX_ANYTHING</docker.image.prefix>


<build>
        <finalName>alibaba-cloud-gateway</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>

                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>

            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <version>1.4.10</version>
                <configuration>
                    <repository>${docker.image.prefix}/${project.artifactId}</repository>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

- Spotify 的 docker-maven-plugin 插件是用maven插件方式构建docker镜像的。

```
${project.build.finalName} 产出物名称，缺省为${project.artifactId}-${project.version}
```

#### 打包

- 创建Dockerfile,默认是根目录，（可以修改为src/main/docker/Dockerfile,如果修则需要制定路径）

- 什么是Dockerfile

  - 由一系列命令和参数构成的脚本，这些命令应用于基础镜像, 最终创建一个新的镜像

  ```
  FROM  adoptopenjdk/openjdk11:ubi
  VOLUME /tmp
  ARG JAR_FILE
  COPY ${JAR_FILE} app.jar
  ENTRYPOINT ["java","-jar","/app.jar"]
  
  
  FROM <image>:<tag> 需要一个基础镜像，可以是公共的或者是私有的，
  后续构建会基于此镜像，如果同一个Dockerfile中建立多个镜像时，可以使用多个FROM指令
        
  VOLUME  配置一个具有持久化功能的目录，主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp。改步骤是可选的，如果涉及到文件系统的应用就很有必要了。
  /tmp目录用来持久化到 Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用/tmp作为工作目录 
  
  ARG  设置编译镜像时加入的参数， JAR_FILE 是设置容器的环境变量(maven里面配置的)
  COPY : 只支持将本地文件复制到容器 ,还有个ADD更强大但复杂点
  ENTRYPOINT 容器启动时执行的命令
  
  EXPOSE 8080 暴露镜像端口
  ```

- 构建镜像( 去到子模块pom文件下，不然启动时会报错)

  ```
  mvn install -Dmaven.test.skip=true dockerfile:build
  //no main manifest attribute, in /app.jar   xxx.jar中没有主清单属性
  ```

##### 推送仓库

- https://cr.console.aliyun.com/repository/cn-beijing/zyaire-cloud/gateway/details

  自己阿里云容器镜像仓库有操作手册

#### 部署

##### docker部署nacos

- 拉取特别慢

```
路径/etc/docker/daemon.json 增加下面的配置
{
  "registry-mirrors": ["https://pb5bklzr.mirror.aliyuncs.com"]
}

//重启
sudo systemctl daemon-reload
sudo systemctl restart docker
```

- docker拉取镜像

```
docker pull nacos/nacos-server
```

- 查看镜像

```
docker images
```

- 启动Nacos

```
docker run --env MODE=standalone --name xdclass-nacos -d -p 8848:8848 ef8e53226440 (镜像id)

//查看日志
docker logs -f 
```

- 访问Nacos（记得开放阿里云的网络安全组）

```
http://公网ip:8848/nacos

# 登录密码默认nacos/nacos
```

- 注意docker要启动

```
[root@zyaire ~]# docker pull nacos/nacos-server
Using default tag: latest
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

- 机器配置，nacos安装在高配机器

```
2 vCPU 1 GiB (安装了Mysql/Zipkin服务)

2 vCPU 4 GiB（安装了Nacos、Sentinel、网关、视频服务、订单服务）
```

##### docker部署sentinel

- docker拉取镜像

```
docker pull bladex/sentinel-dashboard:latest
```

- 查看镜像

```
docker images
```

- 启动Sentinel

```
docker run --name sentinel -d -p 8858:8858  镜像id
```

- 访问Sentinel（记得开放阿里云的网络安全组）

```
http://公网ip:8858

# 登录密码默认sentinel/sentinel
```

- 机器配置, 安装在高配机器(就是微服务同个宿主机)

```
2 vCPU 1 GiB (安装了Mysql/Zipkin服务)

2 vCPU 4 GiB（安装了Nacos、Sentinel、网关、视频服务、订单服务）
```

##### Docker部署Zipkin

- docker拉取镜像

```
docker pull openzipkin/zipkin:latest
```

- 查看镜像

```
docker images
```

- 启动Zipkin

```
docker run --name xdclass-zipkin -d -p 9411:9411 镜像id
```

- 访问zipkin（记得开放阿里云的网络安全组）

```
http://公网ip:9411/zipkin/
```

- 机器配置, 部署在低配服务器

```
120.24.216.117(公)

2 vCPU 4 GiB（安装了Nacos、Sentinel、网关、视频服务、订单服务）
```

##### 快速安装mysql

```
#下载mysql的Yum仓库
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

yum -y install mysql57-community-release-el7-10.noarch.rpm

#安装 mysql服务
yum -y install mysql-community-server

#启动数据库服务， systemctl 该命令可用于查看系统状态和管理系统及服务，centos7上开始使用
systemctl start  mysqld.service

#查看状态
systemctl status mysqld.service

#在日志文件中查看初始密码
grep "password" /var/log/mysqld.log

#进入修改Mysql密码

mysql -uroot -p

#新密码设置必须由大小写字母、数字和特殊符号组成
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Xdclass.net168';

#开启mysql的远程访问， %是指全部
grant all privileges on *.* to 'root'@'%' identified by 'Xdclass.net168' with grant option;

#刷新权限
flush privileges; 
```

##### 开启

- 记得打开网关的端口