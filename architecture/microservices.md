# 架构设计核心知识

## 微服务架构

### 1. 基本概念

| 概念 | 描述 |
| :--- | :--- |
| **微服务** | 将单一应用拆分为多个小型服务，每个服务独立部署和扩展 |
| **服务治理** | 服务的注册、发现、负载均衡、熔断、限流等 |
| **API网关** | 统一入口，处理请求路由、认证、监控等 |
| **服务注册与发现** | 服务实例的注册和发现机制 |
| **配置中心** | 集中管理配置，支持动态刷新 |
| **熔断机制** | 防止服务级联失败 |
| **限流机制** | 控制请求速率，保护系统 |

### 2. 微服务核心组件

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │
│  API网关    ├────▶│  服务A      ├────▶│  服务B      │
│             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘
         ▲                  ▲                  ▲
         │                  │                  │
         ▼                  ▼                  ▼
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│             │     │             │     │             │
│  配置中心   │     │  服务注册中心│     │  监控中心   │
│             │     │             │     │             │
└─────────────┘     └─────────────┘     └─────────────┘
```

### 3. 服务注册与发现

| 技术 | 类型 | 特点 |
| :--- | :--- | :--- |
| **Eureka** | 客户端发现 | 基于REST，自我保护机制，Spring Cloud生态 |
| **Consul** | 客户端/服务器发现 | 健康检查，KV存储，多数据中心支持 |
| **ZooKeeper** | 服务器发现 | 强一致性，分布式协调服务 |
| **Nacos** | 客户端/服务器发现 | 动态配置，服务治理，支持DNS和HTTP |

### 4. API网关

| 技术 | 特点 |
| :--- | :--- |
| **Spring Cloud Gateway** | 基于WebFlux，响应式，Spring Cloud生态 |
| **Zuul** | 基于Servlet，阻塞式，Netflix OSS |
| **Kong** | 基于Nginx，高性能，插件化 |
| **Traefik** | 自动服务发现，支持多种后端 |

## 系统设计原则

### 1. 单一职责原则 (SRP)

> 一个类或模块应该只有一个引起它变化的原因。

### 2. 开放封闭原则 (OCP)

> 软件实体（类、模块、函数等）应该对扩展开放，对修改封闭。

### 3. 里氏替换原则 (LSP)

> 子类型必须能够替换掉它们的父类型。

### 4. 接口隔离原则 (ISP)

> 客户端不应该依赖它不需要的接口。

### 5. 依赖倒置原则 (DIP)

> 高层模块不应该依赖低层模块，它们都应该依赖抽象。
> 抽象不应该依赖细节，细节应该依赖抽象。

### 6. 其他重要原则

- **KISS原则**：Keep It Simple, Stupid
- **YAGNI原则**：You Aren't Gonna Need It
- **DRY原则**：Don't Repeat Yourself
- **高内聚低耦合**：模块内部紧密相关，模块之间松耦合
- **关注点分离**：不同功能模块分离，便于维护和扩展

## 设计模式

### 1. 创建型模式

#### 1.1 单例模式 (Singleton)

```java
// 双重检查锁定模式
public class Singleton {
    private volatile static Singleton instance;
    
    private Singleton() {
        // 私有构造方法
    }
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

#### 1.2 工厂模式 (Factory)

```java
// 工厂方法模式
public interface ShapeFactory {
    Shape createShape();
}

public class CircleFactory implements ShapeFactory {
    @Override
    public Shape createShape() {
        return new Circle();
    }
}

public class RectangleFactory implements ShapeFactory {
    @Override
    public Shape createShape() {
        return new Rectangle();
    }
}
```

#### 1.3 建造者模式 (Builder)

```java
public class User {
    private final String firstName;
    private final String lastName;
    private final int age;
    private final String email;
    
    private User(Builder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.email = builder.email;
    }
    
    public static class Builder {
        private final String firstName;
        private final String lastName;
        private int age;
        private String email;
        
        public Builder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        
        public Builder age(int age) {
            this.age = age;
            return this;
        }
        
        public Builder email(String email) {
            this.email = email;
            return this;
        }
        
        public User build() {
            return new User(this);
        }
    }
}

// 使用
User user = new User.Builder("John", "Doe")
        .age(30)
        .email("john.doe@example.com")
        .build();
```

### 2. 结构型模式

#### 2.1 适配器模式 (Adapter)

```java
// 目标接口
public interface Target {
    void request();
}

// 适配者类
public class Adaptee {
    public void specificRequest() {
        System.out.println("Adaptee's specific request");
    }
}

// 适配器类
public class Adapter implements Target {
    private Adaptee adaptee;
    
    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }
    
    @Override
    public void request() {
        adaptee.specificRequest();
    }
}
```

#### 2.2 装饰器模式 (Decorator)

```java
// 抽象组件
public interface Beverage {
    String getDescription();
    double cost();
}

// 具体组件
public class Espresso implements Beverage {
    @Override
    public String getDescription() {
        return "Espresso";
    }
    
    @Override
    public double cost() {
        return 1.99;
    }
}

// 装饰器抽象类
public abstract class CondimentDecorator implements Beverage {
    protected Beverage beverage;
    
    public CondimentDecorator(Beverage beverage) {
        this.beverage = beverage;
    }
}

// 具体装饰器
public class Milk extends CondimentDecorator {
    public Milk(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Milk";
    }
    
    @Override
    public double cost() {
        return beverage.cost() + 0.20;
    }
}
```

#### 2.3 代理模式 (Proxy)

```java
// 接口
public interface Image {
    void display();
}

// 真实对象
public class RealImage implements Image {
    private String fileName;
    
    public RealImage(String fileName) {
        this.fileName = fileName;
        loadFromDisk(fileName);
    }
    
    private void loadFromDisk(String fileName) {
        System.out.println("Loading " + fileName);
    }
    
    @Override
    public void display() {
        System.out.println("Displaying " + fileName);
    }
}

// 代理对象
public class ProxyImage implements Image {
    private RealImage realImage;
    private String fileName;
    
    public ProxyImage(String fileName) {
        this.fileName = fileName;
    }
    
    @Override
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(fileName);
        }
        realImage.display();
    }
}
```

### 3. 行为型模式

#### 3.1 观察者模式 (Observer)

```java
// 主题接口
public interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// 观察者接口
public interface Observer {
    void update(float temperature, float humidity, float pressure);
}

// 具体主题
public class WeatherData implements Subject {
    private List<Observer> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherData() {
        observers = new ArrayList<>();
    }
    
    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }
    
    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }
    
    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }
    
    public void measurementsChanged() {
        notifyObservers();
    }
    
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        measurementsChanged();
    }
}
```

#### 3.2 策略模式 (Strategy)

```java
// 策略接口
public interface PaymentStrategy {
    void pay(int amount);
}

// 具体策略
public class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    private String cvv;
    private String expiryDate;
    
    public CreditCardPayment(String cardNumber, String cvv, String expiryDate) {
        this.cardNumber = cardNumber;
        this.cvv = cvv;
        this.expiryDate = expiryDate;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid with credit card: " + cardNumber);
    }
}

public class PayPalPayment implements PaymentStrategy {
    private String email;
    private String password;
    
    public PayPalPayment(String email, String password) {
        this.email = email;
        this.password = password;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid with PayPal: " + email);
    }
}

// 上下文类
public class ShoppingCart {
    private List<Item> items;
    private PaymentStrategy paymentStrategy;
    
    public ShoppingCart() {
        items = new ArrayList<>();
    }
    
    public void addItem(Item item) {
        items.add(item);
    }
    
    public void removeItem(Item item) {
        items.remove(item);
    }
    
    public int calculateTotal() {
        return items.stream().mapToInt(Item::getPrice).sum();
    }
    
    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }
    
    public void checkout() {
        int total = calculateTotal();
        paymentStrategy.pay(total);
    }
}
```

#### 3.3 模板方法模式 (Template Method)

```java
// 抽象类
public abstract class Game {
    abstract void initialize();
    abstract void startPlay();
    abstract void endPlay();
    
    // 模板方法
    public final void play() {
        // 初始化游戏
        initialize();
        
        // 开始游戏
        startPlay();
        
        // 结束游戏
        endPlay();
    }
}

// 具体实现
public class Cricket extends Game {
    @Override
    void initialize() {
        System.out.println("Cricket Game Initialized! Start playing.");
    }
    
    @Override
    void startPlay() {
        System.out.println("Cricket Game Started. Enjoy the game!");
    }
    
    @Override
    void endPlay() {
        System.out.println("Cricket Game Finished!");
    }
}

public class Football extends Game {
    @Override
    void initialize() {
        System.out.println("Football Game Initialized! Start playing.");
    }
    
    @Override
    void startPlay() {
        System.out.println("Football Game Started. Enjoy the game!");
    }
    
    @Override
    void endPlay() {
        System.out.println("Football Game Finished!");
    }
}
```

## 分布式系统设计

### 1. 分布式系统的挑战

| 挑战 | 描述 |
| :--- | :--- |
| **网络延迟** | 网络通信存在延迟 |
| **网络分区** | 网络故障导致节点间无法通信 |
| **节点故障** | 单个节点可能故障 |
| **一致性问题** | 数据一致性难以保证 |
| **安全性** | 分布式环境下的安全问题 |

### 2. CAP定理

- **C (Consistency)**：一致性，所有节点同时看到相同的数据
- **A (Availability)**：可用性，系统在任何时候都能响应请求
- **P (Partition tolerance)**：分区容错性，系统在网络分区时仍能继续运行

> CAP定理指出，分布式系统最多只能同时满足其中两个特性。

### 3. BASE理论

- **Basically Available**：基本可用，系统在出现故障时仍能提供部分服务
- **Soft State**：软状态，允许系统存在中间状态
- **Eventually Consistent**：最终一致性，系统最终会达到一致状态

### 4. 分布式事务

| 方案 | 描述 | 优缺点 |
| :--- | :--- | :--- |
| **2PC (两阶段提交)** | 协调者协调各参与者，分准备和提交两个阶段 | 一致性好，但性能差，存在阻塞问题 |
| **3PC (三阶段提交)** | 增加了准备阶段，减少阻塞风险 | 比2PC更可靠，但仍存在一致性问题 |
| **TCC (Try-Confirm-Cancel)** | 业务层面的两阶段提交 | 性能好，灵活性高，但实现复杂 |
| **SAGA模式** | 一系列本地事务，失败时执行补偿操作 | 适合长事务，实现复杂 |
| **本地消息表** | 消息持久化到本地，异步处理 | 可靠性高，实现简单 |

## 系统性能优化

### 1. 性能优化原则

- **定位瓶颈**：使用监控工具找到性能瓶颈
- **优先优化**：优先优化影响最大的瓶颈
- **数据驱动**：基于数据进行优化，避免猜测
- **持续优化**：性能优化是持续过程

### 2. 常见优化手段

| 层次 | 优化手段 |
| :--- | :--- |
| **应用层** | 代码优化、缓存、异步处理、负载均衡 |
| **数据库层** | 索引优化、查询优化、分库分表、读写分离 |
| **网络层** | CDN加速、连接池、HTTP/2、WebSocket |
| **系统层** | 操作系统调优、JVM调优、容器资源配置 |

### 3. 缓存策略

- **缓存位置**：浏览器缓存、CDN缓存、应用缓存、数据库缓存
- **缓存更新策略**：
  - **Cache-Aside**：先更新数据库，再删除缓存
  - **Write-Through**：先更新缓存，再更新数据库
  - **Write-Back**：先更新缓存，异步更新数据库
- **缓存穿透**：缓存和数据库都没有的数据，使用布隆过滤器或缓存空结果
- **缓存击穿**：热点数据过期，使用互斥锁或预加载
- **缓存雪崩**：大量缓存同时过期，使用随机过期时间或分布式锁

### 4. 数据库优化

- **索引优化**：
  - 为频繁查询的字段添加索引
  - 避免过多索引
  - 定期优化索引
- **查询优化**：
  - 避免SELECT *
  - 使用JOIN代替子查询
  - 合理使用分页
- **分库分表**：
  - 垂直分库：按功能模块拆分
  - 垂直分表：按列拆分
  - 水平分库：按行拆分
  - 水平分表：按行拆分
- **读写分离**：主库写，从库读

## 安全性设计

### 1. 常见安全威胁

| 威胁 | 描述 |
| :--- | :--- |
| **SQL注入** | 通过注入SQL语句攻击数据库 |
| **XSS (跨站脚本)** | 注入恶意脚本到网页 |
| **CSRF (跨站请求伪造)** | 伪造用户请求 |
| **身份认证漏洞** | 认证机制缺陷 |
| **授权漏洞** | 授权机制缺陷 |
| **敏感数据泄露** | 敏感数据未加密存储 |
| **DDoS攻击** | 分布式拒绝服务攻击 |

### 2. 安全防御措施

- **身份认证**：
  - 使用强密码策略
  - 多因素认证
  - 单点登录 (SSO)
- **授权**：
  - 基于角色的访问控制 (RBAC)
  - 最小权限原则
  - 定期权限审计
- **数据安全**：
  - 敏感数据加密存储
  - HTTPS传输
  - 数据脱敏
- **输入验证**：
  - 所有输入都要验证
  - 使用参数化查询防止SQL注入
  - 过滤用户输入防止XSS
- **防护措施**：
  - 使用Web应用防火墙 (WAF)
  - 实施速率限制
  - 定期安全扫描
  - 安全日志和监控

@author wujingkai