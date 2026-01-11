# JavaScript 核心知识

## 基础语法

### 1. 变量声明

```javascript
// var - 函数作用域，存在变量提升
var name = "John";

// let - 块级作用域，不存在变量提升
let age = 30;

// const - 块级作用域，常量
const PI = 3.14;
```

### 2. 数据类型

| 类型 | 描述 | 示例 |
| :--- | :--- | :--- |
| **基本类型** | 直接存储值 | `string`, `number`, `boolean`, `null`, `undefined`, `symbol`, `bigint` |
| **引用类型** | 存储引用地址 | `object`, `array`, `function`, `date`, `regexp` |

### 3. 函数

```javascript
// 函数声明
function add(a, b) {
    return a + b;
}

// 函数表达式
const multiply = function(a, b) {
    return a * b;
};

// 箭头函数
const divide = (a, b) => a / b;

// 函数参数默认值
function greet(name = "Guest") {
    return `Hello, ${name}!`;
}
```

## DOM 操作

### 1. 选择元素

```javascript
// 通过ID选择
const element = document.getElementById("myId");

// 通过类名选择
const elements = document.getElementsByClassName("myClass");

// 通过标签名选择
const divs = document.getElementsByTagName("div");

// 通过CSS选择器选择
const firstElement = document.querySelector(".myClass");
const allElements = document.querySelectorAll(".myClass");
```

### 2. 修改元素

```javascript
// 修改内容
const element = document.getElementById("myId");
element.innerHTML = "<h1>Hello World</h1>";
element.textContent = "Hello World";

// 修改样式
element.style.color = "red";
element.style.fontSize = "20px";

// 添加/移除类
element.classList.add("active");
element.classList.remove("active");
element.classList.toggle("active");
```

### 3. 事件处理

```javascript
// 内联事件（不推荐）
// <button onclick="handleClick()">点击我</button>

// DOM属性绑定
element.onclick = function() {
    console.log("按钮被点击了");
};

// addEventListener（推荐）
element.addEventListener("click", function() {
    console.log("按钮被点击了");
});

// 移除事件
element.removeEventListener("click", handleClick);
```

## 异步编程

### 1. 回调函数

```javascript
function fetchData(callback) {
    setTimeout(() => {
        callback(null, "数据");
    }, 1000);
}

fetchData((error, data) => {
    if (error) {
        console.error(error);
        return;
    }
    console.log(data);
});
```

### 2. Promise

```javascript
function fetchData() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("数据");
            // reject(new Error("获取数据失败"));
        }, 1000);
    });
}

// 使用Promise
fetchData()
    .then(data => {
        console.log(data);
    })
    .catch(error => {
        console.error(error);
    });
```

### 3. async/await

```javascript
async function fetchData() {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve("数据");
        }, 1000);
    });
}

async function getData() {
    try {
        const data = await fetchData();
        console.log(data);
    } catch (error) {
        console.error(error);
    }
}

getData();
```

## ES6+ 特性

### 1. 解构赋值

```javascript
// 对象解构
const person = { name: "John", age: 30 };
const { name, age } = person;

// 数组解构
const numbers = [1, 2, 3];
const [a, b, c] = numbers;

// 参数解构
function greet({ name, age }) {
    return `Hello, ${name}, you are ${age} years old.`;
}
```

### 2. 扩展运算符

```javascript
// 数组扩展
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5, 6];

// 对象扩展
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3, d: 4 };

// 函数参数扩展
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}
```

### 3. 模板字符串

```javascript
const name = "John";
const age = 30;
const message = `Hello, ${name}! You are ${age} years old.`;
```

### 4. 类

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    greet() {
        return `Hello, my name is ${this.name}.`;
    }

    static createPerson(name, age) {
        return new Person(name, age);
    }
}

// 继承
class Student extends Person {
    constructor(name, age, grade) {
        super(name, age);
        this.grade = grade;
    }

    study() {
        return `${this.name} is studying in grade ${this.grade}.`;
    }
}
```

## 常见设计模式

### 1. 单例模式

```javascript
const Singleton = (function() {
    let instance;

    function createInstance() {
        const object = new Object("我是单例实例");
        return object;
    }

    return {
        getInstance: function() {
            if (!instance) {
                instance = createInstance();
            }
            return instance;
        }
    };
})();
```

### 2. 工厂模式

```javascript
function createPerson(name, age) {
    return {
        name: name,
        age: age,
        greet: function() {
            return `Hello, my name is ${this.name}.`;
        }
    };
}
```

### 3. 观察者模式

```javascript
class Observer {
    constructor() {
        this.observers = [];
    }

    subscribe(fn) {
        this.observers.push(fn);
    }

    unsubscribe(fn) {
        this.observers = this.observers.filter(subscriber => subscriber !== fn);
    }

    notify(data) {
        this.observers.forEach(observer => observer(data));
    }
}
```

## 常见问题与解决方案

### 1. 类型转换

```javascript
// 显式转换
const str = String(123);
const num = Number("123");
const bool = Boolean(0);

// 隐式转换
const sum = "123" + 456; // "123456"
const diff = "123" - 456; // -333
```

### 2. this 指向

```javascript
// 函数中的this指向全局对象
function showThis() {
    console.log(this);
}

// 对象方法中的this指向该对象
const obj = {
    name: "John",
    showName: function() {
        console.log(this.name);
    }
};

// 箭头函数中的this指向外层作用域
const obj = {
    name: "John",
    showName: () => {
        console.log(this.name); // 指向外层作用域的this
    }
};
```

### 3. 内存泄漏

```javascript
// 解决方法：及时清除事件监听器
function addListener() {
    const element = document.getElementById("myButton");
    element.addEventListener("click", handleClick);
}

function removeListener() {
    const element = document.getElementById("myButton");
    element.removeEventListener("click", handleClick);
}
```

@author wujingkai