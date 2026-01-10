- [PDF书籍](https://wujingkai-nsm.github.io/pdfs)
- [常用链接工具](https://wujingkai-nsm.github.io/fav-links)

# 常用工具的应用指南

## Eureka

**消费者该如何获取服务提供者具体信息？**

1. 服务提供者启动时向eureka注册自己的信息
2. eureka保存这些信息
3. 消费者根据服务名称向eureka拉取提供者信息

**如果有多个服务提供者，消费者该如何选择？**

1. 服务消费者利用负载均衡算法，从服务列表中挑选一个

**消费者如何感知服务提供者健康状态？**

1. 服务提供者会每隔30秒向eurekaservice发送心跳请求，报考健康状态
2. eureka会更新记录服务列表信息，心跳不正常会被剔除
3. 消费者就可以拉取到最新的信息

### 服务端)

1. 创建一个maven项目
2. 在中maven添加需要的包

```pom
<!-- eureka服务端-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

1. 编写主启动类

```java
/*
* 该函数是用于在Java应用程序中启用Eureka Server功能的关键注解。
* Eureka Server是一个注册中心，用于在微服务架构中管理服务实例，
* 并提供服务发现、负载均衡和监控等功能。通过此注解，
* 应用程序可以充当Eureka Server，并提供注册和发现服务的功能。
* */
@EnableEurekaServer
@SpringBootApplication
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class,args);
    }
}
```

1. 编写application.yml配置类

```yaml
server:
  port: 9010 #服务端口号
spring:
  application:
    name: eurekaserver #服务名称
eureka:
  client:
    service-url: # eureka的地址信息
      defaultZone: http://localhost:9010/eureka/ #注册中心地址
#  instance:
#    prefer-ip-address: true #使用ip地址注册到注册中心
#    instance-id: ${spring.application.name}-${random.value} #服务实例id
#    lease-renewal-interval-in-seconds: 5 #心跳时间间隔
#    lease-expiration-duration-in-seconds: 10 #失效时间间隔
```

### 客户端

1. 在maven中添加依赖

```pom
<!--eureka客户端-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

1. 添加配置文件application.yml

```yaml
eureka:
  client:
    service-url: # eureka的地址信息
      defaultZone: http://localhost:8081/eureka/ #注册中心地址
```

### 启动多个服务

我们可以将user-service多次启动，模拟多实例部署，但为了避免端口冲突，需要修改端口设置：

设置端口

```
-Dserver.port=8082
```

### 负载均衡

服务拉取是基于服务名称获取服务列表，然后在对服务列表做负载均衡

1. 修改OrederService的代码，修改访问的url路径，用服务名代替ip，端口：

```java
 //以前的调用方式
String url = "http://localhost:8081/user/"+order.getUserId();
 //现在的调用方式
String url = "http://userservice/user/"+order.getUserId();
```

1. 在order-servie项目的启动类OrderApplication中的RestTemplate添加负载均衡注解(在客户端主启动类中添加)：

```java
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

### Ribbon负载均衡

| 内置负载均衡规则类        | 描述规则                                                     |
| :------------------------ | :----------------------------------------------------------- |
| RounRobinRule             | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则   |
| AvailabilityFilteringRule | 对一下两种服务器进行忽略： （1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路转台就持续30秒，如果再次连接失败，短路的持续时间就会几何地增加. （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置AvailabilityFilterngRule规则的客户端也会将其忽略。并发连接数的上线，可以由客户端的..ActiceConnectionslimit属性进行配置 |
| WeightedResponseTimeRule  | 为每个服务器赋予一个权重值。服务器响应时长越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重会影响服务器的选择。 |
| ZoneAvoidanceRule         | 以区域可用的服务器为基础进行服务器选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房，一个机架等。而后在对Zone内对各服务做轮询 |
| BestAvailableRule         | 忽略哪些短路的服务器，并选择并发数较低的服务器。             |
| RandomRule                | 随机选择一个可用的服务器                                     |
| RetryRule                 | 重试机制的选择逻辑                                           |

### 修改轮询策略

通过定义`IRule`实现可以修改负载均衡规则，有两种方式：

1. 代码方式：在order-service中的OrderApplication类中，定义一个新的IRule（在主配置类中添加下面代码确定轮询机制）针对全体对象：

```java
@Bean
public IRule randomRule() {
    return new RandomRule();
}
```

1. 可以在application配置文件里配置针对userservice对象

```yaml'
userservice:
  ribbon:
    NFLoadbalancerClassName: com.netflix.loadbalancer.RandomRule
```

### 饥饿加载

Ribbon默认是采用懒加载，即第一次访问时才去创建LoadBalanceClient，请求时间会很长。饥饿加载则会在项目启动时创建，将第一次访问的耗时，通过下面配置开启饥饿加载：

```
ribbon:
  eager-load:
    enabled: true #开启饥饿加载
    clients:
      - userservice
```

开启饥饿加载  快300毫秒左右（未开启1300毫秒开启后500左右毫秒）
