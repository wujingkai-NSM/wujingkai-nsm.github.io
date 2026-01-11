# Vue 核心知识

## 基础概念

### 1. Vue 特点

- **渐进式框架**：可以逐步集成到现有项目中
- **响应式数据绑定**：数据变化自动更新视图
- **组件化开发**：将UI拆分为独立可复用的组件
- **指令系统**：提供丰富的内置指令
- **虚拟DOM**：提高渲染性能

### 2. Vue 实例

```javascript
const app = new Vue({
    el: '#app', // 挂载点
    data: {
        message: 'Hello Vue!',
        count: 0
    },
    methods: {
        increment() {
            this.count++;
        }
    },
    computed: {
        reversedMessage() {
            return this.message.split('').reverse().join('');
        }
    },
    watch: {
        count(newValue, oldValue) {
            console.log(`Count changed from ${oldValue} to ${newValue}`);
        }
    }
});
```

## 模板语法

### 1. 插值

```html
<!-- 文本插值 -->
<p>{{ message }}</p>

<!-- 原始HTML -->
<p v-html="rawHtml"></p>

<!-- 属性绑定 -->
<img v-bind:src="imageSrc" alt="">
<!-- 简写 -->
<img :src="imageSrc" alt="">

<!-- 表达式 -->
<p>{{ message.split('').reverse().join('') }}</p>
<p>{{ count > 10 ? '大于10' : '小于等于10' }}</p>
```

### 2. 指令

```html
<!-- 条件渲染 -->
<p v-if="seen">现在你看到我了</p>
<p v-else-if="condition">否则如果</p>
<p v-else>否则</p>

<!-- 列表渲染 -->
<ul>
    <li v-for="(item, index) in items" :key="item.id">
        {{ index }}: {{ item.text }}
    </li>
</ul>

<!-- 事件监听 -->
<button v-on:click="increment">增加</button>
<!-- 简写 -->
<button @click="increment">增加</button>

<!-- 事件修饰符 -->
<button @click.stop="doThis">阻止冒泡</button>
<input @keyup.enter="submit">

<!-- 表单绑定 -->
<input v-model="message" placeholder="编辑我...">
<select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
</select>
```

## 组件开发

### 1. 组件注册

```javascript
// 全局注册
Vue.component('my-component', {
    template: '<div>A custom component!</div>'
});

// 局部注册
const MyComponent = {
    template: '<div>A custom component!</div>'
};

new Vue({
    el: '#app',
    components: {
        'my-component': MyComponent
    }
});
```

### 2. 组件通信

#### 2.1 父传子（Props）

```javascript
// 父组件
const ParentComponent = {
    template: '<child-component :message="parentMessage"></child-component>',
    data() {
        return {
            parentMessage: 'Hello from parent'
        };
    }
};

// 子组件
const ChildComponent = {
    template: '<div>{{ message }}</div>',
    props: ['message']
    // 或带验证的props
    props: {
        message: {
            type: String,
            required: true,
            default: 'Default message'
        }
    }
};
```

#### 2.2 子传父（Events）

```javascript
// 子组件
const ChildComponent = {
    template: '<button @click="sendMessage">发送消息</button>',
    methods: {
        sendMessage() {
            this.$emit('message-sent', 'Hello from child');
        }
    }
};

// 父组件
const ParentComponent = {
    template: '<child-component @message-sent="handleMessage"></child-component>',
    methods: {
        handleMessage(message) {
            console.log(message); // 输出：Hello from child
        }
    }
};
```

#### 2.3 兄弟组件通信（Event Bus）

```javascript
// 创建事件总线
const bus = new Vue();

// 组件A（发送方）
bus.$emit('event-name', data);

// 组件B（接收方）
bus.$on('event-name', (data) => {
    console.log(data);
});
```

## Vue 3 Composition API

### 1. setup 函数

```vue
<template>
    <div>
        <p>{{ count }}</p>
        <button @click="increment">增加</button>
    </div>
</template>

<script>
import { ref, computed, watch, onMounted } from 'vue';

export default {
    setup() {
        // 响应式数据
        const count = ref(0);
        
        // 方法
        const increment = () => {
            count.value++;
        };
        
        // 计算属性
        const doubledCount = computed(() => count.value * 2);
        
        // 监听器
        watch(count, (newValue, oldValue) => {
            console.log(`Count changed from ${oldValue} to ${newValue}`);
        });
        
        // 生命周期钩子
        onMounted(() => {
            console.log('Component mounted');
        });
        
        // 返回给模板使用
        return {
            count,
            increment,
            doubledCount
        };
    }
};
</script>
```

### 2. 响应式API

```javascript
import { ref, reactive, toRefs, computed } from 'vue';

// 基本类型响应式
const count = ref(0);
count.value++; // 访问和修改

// 对象类型响应式
const user = reactive({
    name: 'John',
    age: 30
});
user.name = 'Jane'; // 直接修改

// 解构响应式对象
const { name, age } = toRefs(user);

// 计算属性
const fullName = computed(() => {
    return `${user.firstName} ${user.lastName}`;
});
```

## Vue Router

### 1. 基本配置

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router';

Vue.use(VueRouter);

const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home
    },
    {
        path: '/about',
        name: 'About',
        component: () => import('../views/About.vue') // 懒加载
    },
    {
        path: '/users/:id',
        name: 'UserDetail',
        component: UserDetail,
        props: true // 将路由参数作为props传递
    }
];

const router = new VueRouter({
    mode: 'history', // 去除#号
    base: process.env.BASE_URL,
    routes
});

export default router;
```

### 2. 路由导航

```html
<!-- 声明式导航 -->
<router-link to="/">首页</router-link>
<router-link :to="{ name: 'UserDetail', params: { id: 123 } }">用户详情</router-link>

<!-- 编程式导航 -->
<button @click="$router.push('/about')">前往关于页</button>
<button @click="$router.replace('/about')">替换当前路由</button>
<button @click="$router.go(-1)">返回上一页</button>
```

## Vuex 状态管理

### 1. 核心概念

```javascript
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
    state: {
        count: 0,
        user: null
    },
    mutations: {
        increment(state) {
            state.count++;
        },
        setUser(state, user) {
            state.user = user;
        }
    },
    actions: {
        incrementAsync({ commit }) {
            setTimeout(() => {
                commit('increment');
            }, 1000);
        },
        login({ commit }, userData) {
            return new Promise((resolve) => {
                // 模拟API请求
                setTimeout(() => {
                    commit('setUser', userData);
                    resolve();
                }, 1000);
            });
        }
    },
    getters: {
        doubleCount: state => state.count * 2,
        isLoggedIn: state => !!state.user
    },
    modules: {
        // 模块化状态管理
        products: productModule
    }
});
```

### 2. 使用Vuex

```javascript
// 在组件中使用
const CounterComponent = {
    template: `
        <div>
            <p>{{ count }}</p>
            <p>{{ doubleCount }}</p>
            <button @click="increment">增加</button>
            <button @click="incrementAsync">异步增加</button>
        </div>
    `,
    computed: {
        count() {
            return this.$store.state.count;
        },
        doubleCount() {
            return this.$store.getters.doubleCount;
        }
    },
    methods: {
        increment() {
            this.$store.commit('increment');
        },
        incrementAsync() {
            this.$store.dispatch('incrementAsync');
        }
    }
};
```

## 性能优化

### 1. 列表渲染优化

```html
<!-- 为列表项添加唯一key -->
<ul>
    <li v-for="item in items" :key="item.id">
        {{ item.name }}
    </li>
</ul>

<!-- 使用v-once减少不必要的渲染 -->
<span v-once>This will never change: {{ msg }}</span>
```

### 2. 计算属性缓存

```javascript
// 使用计算属性缓存结果，避免重复计算
computed: {
    filteredItems() {
        return this.items.filter(item => item.price > this.minPrice);
    }
}
```

### 3. 异步组件和懒加载

```javascript
// 路由懒加载
const About = () => import('../views/About.vue');

// 组件懒加载
const AsyncComponent = () => import('./AsyncComponent.vue');
```

## 常见问题与解决方案

### 1. 避免直接修改状态

```javascript
// 错误（Vuex）
this.$store.state.count++;

// 正确（Vuex）
this.$store.commit('increment');

// 错误（Composition API）
count++;

// 正确（Composition API）
count.value++;
```

### 2. 解决v-for和v-if同时使用的问题

```html
<!-- 不推荐：v-for优先级高于v-if -->
<li v-for="item in items" v-if="item.active" :key="item.id">
    {{ item.text }}
</li>

<!-- 推荐：使用computed先过滤 -->
<li v-for="item in activeItems" :key="item.id">
    {{ item.text }}
</li>
```

### 3. 组件间通信的最佳实践

- **父子组件**：Props向下传递，Events向上传递
- **兄弟组件**：Event Bus或Vuex
- **跨层级组件**：Provide/Inject或Vuex
- **复杂状态管理**：Vuex或Pinia（Vue 3）

@author wujingkai