# Java 核心知识

## 基础语法

### 1. 数据类型

| 类型 | 大小 | 范围 | 默认值 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| **byte** | 1字节 | -128 ~ 127 | 0 | `byte b = 100;` |
| **short** | 2字节 | -32768 ~ 32767 | 0 | `short s = 1000;` |
| **int** | 4字节 | -2^31 ~ 2^31-1 | 0 | `int i = 100000;` |
| **long** | 8字节 | -2^63 ~ 2^63-1 | 0L | `long l = 100000L;` |
| **float** | 4字节 | 单精度 | 0.0f | `float f = 3.14f;` |
| **double** | 8字节 | 双精度 | 0.0d | `double d = 3.14;` |
| **char** | 2字节 | 0 ~ 65535 | '\u0000' | `char c = 'A';` |
| **boolean** | 1位 | true/false | false | `boolean flag = true;` |

### 2. 变量与常量

```java
// 变量声明
int age;
age = 30;

// 变量初始化
int age = 30;

// 常量声明
final int MAX_AGE = 120;

// 类常量
public class Constants {
    public static final double PI = 3.14159;
    public static final String VERSION = "1.0.0";
}
```

### 3. 运算符

| 类型 | 运算符 | 示例 |
| :--- | :--- | :--- |
| **算术运算符** | +, -, *, /, %, ++, -- | `int sum = a + b;` |
| **关系运算符** | ==, !=, >, <, >=, <= | `boolean isEqual = a == b;` |
| **逻辑运算符** | &&, \|\|, ! | `boolean result = a > 0 && b < 10;` |
| **位运算符** | &, \|, ^, ~, <<, >>, >>> | `int result = a & b;` |
| **赋值运算符** | =, +=, -=, *=, /=, %= | `a += 10;` |
| **条件运算符** | ? : | `int max = a > b ? a : b;` |

## 面向对象编程

### 1. 类与对象

```java
// 类定义
public class Person {
    // 属性（成员变量）
    private String name;
    private int age;
    
    // 构造方法
    public Person() {
        // 无参构造
    }
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // 方法
    public void sayHello() {
        System.out.println("Hello, my name is " + name + ".");
    }
    
    // getter和setter方法
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public int getAge() {
        return age;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
}

// 对象创建与使用
Person person = new Person("John", 30);
person.sayHello();
String name = person.getName();
person.setAge(31);
```

### 2. 封装、继承、多态

#### 2.1 封装

```java
public class Student {
    // 私有属性
    private String name;
    private int score;
    
    // 公共方法访问私有属性
    public void setScore(int score) {
        // 数据验证
        if (score >= 0 && score <= 100) {
            this.score = score;
        } else {
            throw new IllegalArgumentException("分数必须在0-100之间");
        }
    }
    
    public int getScore() {
        return score;
    }
}
```

#### 2.2 继承

```java
// 父类
public class Animal {
    protected String name;
    
    public void eat() {
        System.out.println(name + " is eating.");
    }
}

// 子类继承父类
public class Dog extends Animal {
    // 子类特有属性
    private String breed;
    
    public Dog(String name, String breed) {
        this.name = name;
        this.breed = breed;
    }
    
    // 重写父类方法
    @Override
    public void eat() {
        System.out.println(breed + " " + name + " is eating bones.");
    }
    
    // 子类特有方法
    public void bark() {
        System.out.println(name + " is barking.");
    }
}
```

#### 2.3 多态

```java
// 向上转型
Animal animal = new Dog("Buddy", "Golden Retriever");
animal.eat(); // 调用Dog类的eat方法

// 向下转型
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.bark();
}

// 抽象类实现多态
abstract class Shape {
    abstract double area();
}

class Circle extends Shape {
    private double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}

class Rectangle extends Shape {
    private double width;
    private double height;
    
    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    
    @Override
    double area() {
        return width * height;
    }
}
```

### 3. 接口

```java
// 接口定义
public interface Drawable {
    // 抽象方法（默认public abstract）
    void draw();
    
    // 默认方法（Java 8+）
    default void info() {
        System.out.println("This is a drawable object.");
    }
    
    // 静态方法（Java 8+）
    static void description() {
        System.out.println("Drawable interface");
    }
}

// 实现接口
public class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing a circle.");
    }
}

// 实现多个接口
public class Square implements Drawable, Comparable<Square> {
    @Override
    public void draw() {
        System.out.println("Drawing a square.");
    }
    
    @Override
    public int compareTo(Square o) {
        // 比较逻辑
        return 0;
    }
}
```

## 集合框架

### 1. 集合层次结构

- **Collection** 接口
  - **List**：有序、可重复
    - ArrayList：基于动态数组
    - LinkedList：基于双向链表
    - Vector：线程安全的动态数组
  - **Set**：无序、不可重复
    - HashSet：基于哈希表
    - LinkedHashSet：基于哈希表和链表
    - TreeSet：基于红黑树
  - **Queue**：队列，先进先出
    - LinkedList：实现了Queue接口
    - PriorityQueue：优先队列
- **Map** 接口：键值对映射
  - HashMap：基于哈希表
  - LinkedHashMap：基于哈希表和链表
  - TreeMap：基于红黑树
  - Hashtable：线程安全的哈希表

### 2. 常用集合使用

```java
// ArrayList
List<String> names = new ArrayList<>();
names.add("John");
names.add("Jane");
names.add("Bob");
names.remove(1); // 移除索引为1的元素
names.get(0); // 获取索引为0的元素

// HashMap
Map<String, Integer> ages = new HashMap<>();
ages.put("John", 30);
ages.put("Jane", 25);
ages.put("Bob", 35);
ages.get("John"); // 获取John的年龄
ages.containsKey("Bob"); // 检查是否包含Bob
ages.remove("Jane"); // 移除Jane

// HashSet
Set<String> uniqueNames = new HashSet<>();
uniqueNames.add("John");
uniqueNames.add("Jane");
uniqueNames.add("John"); // 重复元素，不会被添加
uniqueNames.contains("John"); // true
```

### 3. 集合遍历

```java
// for循环遍历List
for (int i = 0; i < names.size(); i++) {
    System.out.println(names.get(i));
}

// 增强for循环
for (String name : names) {
    System.out.println(name);
}

// 迭代器遍历
Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    String name = iterator.next();
    System.out.println(name);
    if (name.equals("Bob")) {
        iterator.remove(); // 安全删除元素
    }
}

// forEach方法（Java 8+）
names.forEach(System.out::println);

// 遍历Map
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Stream API（Java 8+）
names.stream()
     .filter(name -> name.startsWith("J"))
     .map(String::toUpperCase)
     .forEach(System.out::println);
```

## 异常处理

### 1. 异常类型

- **Checked Exception**：必须处理的异常，如IOException、SQLException
- **Unchecked Exception**：运行时异常，如NullPointerException、ArrayIndexOutOfBoundsException
- **Error**：严重错误，如OutOfMemoryError、StackOverflowError

### 2. 异常处理机制

```java
// try-catch块
try {
    // 可能抛出异常的代码
    FileReader file = new FileReader("file.txt");
    BufferedReader reader = new BufferedReader(file);
    String line = reader.readLine();
    System.out.println(line);
    reader.close();
} catch (FileNotFoundException e) {
    // 处理文件未找到异常
    System.err.println("File not found: " + e.getMessage());
} catch (IOException e) {
    // 处理IO异常
    System.err.println("IO error: " + e.getMessage());
} finally {
    // 无论是否发生异常，都会执行的代码
    System.out.println("Finally block executed.");
}

// try-with-resources（Java 7+）
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line = reader.readLine();
    System.out.println(line);
} catch (IOException e) {
    System.err.println("IO error: " + e.getMessage());
}

// 自定义异常
public class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

// 抛出异常
public void setAge(int age) throws InvalidAgeException {
    if (age < 0 || age > 120) {
        throw new InvalidAgeException("Age must be between 0 and 120.");
    }
    this.age = age;
}
```

## 多线程编程

### 1. 线程创建

```java
// 方式1：继承Thread类
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

// 使用
MyThread thread = new MyThread();
thread.start();

// 方式2：实现Runnable接口
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

// 使用
Thread thread = new Thread(new MyRunnable());
thread.start();

// 方式3：使用Lambda表达式（Java 8+）
Thread thread = new Thread(() -> {
    System.out.println("Thread running: " + Thread.currentThread().getName());
});
thread.start();

// 方式4：使用Callable和Future
Callable<Integer> task = () -> {
    Thread.sleep(1000);
    return 42;
};

ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(task);

try {
    Integer result = future.get(); // 阻塞等待结果
    System.out.println("Result: " + result);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
} finally {
    executor.shutdown();
}
```

### 2. 线程状态

| 状态 | 描述 |
| :--- | :--- |
| **NEW** | 线程刚创建，尚未启动 |
| **RUNNABLE** | 线程正在执行或就绪 |
| **BLOCKED** | 线程阻塞等待锁 |
| **WAITING** | 线程无限期等待其他线程通知 |
| **TIMED_WAITING** | 线程等待指定时间后自动唤醒 |
| **TERMINATED** | 线程已执行完毕 |

### 3. 线程同步

```java
// synchronized方法
public synchronized void synchronizedMethod() {
    // 同步代码块
}

// synchronized代码块
public void syncBlock() {
    synchronized (this) {
        // 同步代码块
    }
}

// 使用Lock接口
private final Lock lock = new ReentrantLock();

public void lockMethod() {
    lock.lock();
    try {
        // 同步代码
    } finally {
        lock.unlock(); // 确保释放锁
    }
}

// 线程安全的集合
List<String> safeList = Collections.synchronizedList(new ArrayList<>());
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
```

## IO流

### 1. IO流分类

- **按流向**：输入流、输出流
- **按处理数据类型**：字节流、字符流
- **按功能**：节点流、处理流

### 2. 常用IO类

| 类型 | 输入流 | 输出流 |
| :--- | :--- | :--- |
| **字节流** | InputStream | OutputStream |
| **字符流** | Reader | Writer |
| **文件流** | FileInputStream, FileReader | FileOutputStream, FileWriter |
| **缓冲流** | BufferedInputStream, BufferedReader | BufferedOutputStream, BufferedWriter |
| **对象流** | ObjectInputStream | ObjectOutputStream |
| **字节数组流** | ByteArrayInputStream | ByteArrayOutputStream |

### 3. 示例代码

```java
// 字节流读取文件
try (FileInputStream fis = new FileInputStream("file.txt");
     BufferedInputStream bis = new BufferedInputStream(fis)) {
    byte[] buffer = new byte[1024];
    int bytesRead;
    while ((bytesRead = bis.read(buffer)) != -1) {
        System.out.write(buffer, 0, bytesRead);
    }
} catch (IOException e) {
    e.printStackTrace();
}

// 字符流读取文件
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException e) {
    e.printStackTrace();
}

// 字符流写入文件
try (FileWriter fw = new FileWriter("output.txt");
     BufferedWriter bw = new BufferedWriter(fw)) {
    bw.write("Hello, World!");
    bw.newLine();
    bw.write("Welcome to Java IO.");
} catch (IOException e) {
    e.printStackTrace();
}

// 对象序列化
public class Person implements Serializable {
    private static final long serialVersionUID = 1L;
    private String name;
    private int age;
    // getter和setter
}

// 序列化对象
try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person.ser"))) {
    Person person = new Person("John", 30);
    oos.writeObject(person);
    System.out.println("Object serialized successfully.");
} catch (IOException e) {
    e.printStackTrace();
}

// 反序列化对象
try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.ser"))) {
    Person person = (Person) ois.readObject();
    System.out.println("Object deserialized: " + person.getName());
} catch (IOException | ClassNotFoundException e) {
    e.printStackTrace();
}
```

@author wujingkai