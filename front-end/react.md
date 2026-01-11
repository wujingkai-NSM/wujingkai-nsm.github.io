# React 核心知识

## 基础概念

### 1. React 特点

- **声明式**：使用声明式语法描述UI
- **组件化**：将UI拆分为独立可复用的组件
- **单向数据流**：数据从父组件流向子组件
- **虚拟DOM**：提高渲染性能
- **JSX**：JavaScript和HTML的结合

### 2. JSX 语法

```jsx
// JSX语法示例
const element = (
    <div className="container">
        <h1>Hello, {name}!</h1>
        <p>当前时间：{new Date().toLocaleTimeString()}</p>
        {isLoggedIn ? (
            <button onClick={handleLogout}>退出登录</button>
        ) : (
            <button onClick={handleLogin}>登录</button>
        )}
    </div>
);
```

## 组件开发

### 1. 函数组件

```jsx
// 基本函数组件
function Greeting(props) {
    return <h1>Hello, {props.name}!</h1>;
}

// 使用箭头函数
const Greeting = (props) => {
    return <h1>Hello, {props.name}!</h1>;
};

// 解构props
const Greeting = ({ name, age }) => {
    return <h1>Hello, {name}! You are {age} years old.</h1>;
};
```

### 2. 类组件

```jsx
class Greeting extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0 };
        // 绑定this
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        this.setState({ count: this.state.count + 1 });
    }

    render() {
        return (
            <div>
                <h1>Hello, {this.props.name}!</h1>
                <p>Count: {this.state.count}</p>
                <button onClick={this.handleClick}>增加</button>
            </div>
        );
    }
}
```

## Hooks

### 1. useState

```jsx
import React, { useState } from 'react';

function Counter() {
    // 声明一个状态变量count，初始值为0
    // setCount是更新count的函数
    const [count, setCount] = useState(0);
    
    // 功能更新（适用于依赖当前状态的更新）
    const increment = () => {
        setCount(prevCount => prevCount + 1);
    };
    
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={increment}>Click me</button>
        </div>
    );
}
```

### 2. useEffect

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
    const [count, setCount] = useState(0);
    
    // 类似于componentDidMount和componentDidUpdate
    useEffect(() => {
        // 更新文档标题
        document.title = `You clicked ${count} times`;
        
        // 清理函数（类似于componentWillUnmount）
        return () => {
            console.log('组件卸载或依赖项变化');
        };
    }, [count]); // 依赖项数组，只有count变化时才会执行
    
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>Click me</button>
        </div>
    );
}
```

### 3. 其他常用Hooks

```jsx
// useContext - 共享状态
const ThemeContext = React.createContext('light');

function ThemeButton() {
    const theme = useContext(ThemeContext);
    return <button theme={theme}>I am styled by theme context!</button>;
}

// useReducer - 复杂状态管理
function reducer(state, action) {
    switch (action.type) {
        case 'increment':
            return { count: state.count + 1 };
        case 'decrement':
            return { count: state.count - 1 };
        default:
            return state;
    }
}

function Counter() {
    const [state, dispatch] = useReducer(reducer, { count: 0 });
    return (
        <div>
            <p>Count: {state.count}</p>
            <button onClick={() => dispatch({ type: 'increment' })}>+</button>
            <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
        </div>
    );
}

// useCallback - 缓存函数
const memoizedCallback = useCallback(
    () => {
        doSomething(a, b);
    },
    [a, b], // 依赖项
);

// useMemo - 缓存计算结果
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// useRef - 访问DOM或保存可变值
const inputRef = useRef(null);
const previousCountRef = useRef(0);

useEffect(() => {
    previousCountRef.current = count;
});
```

## 状态管理

### 1. React Context

```jsx
// 创建Context
const UserContext = React.createContext();

// 提供者组件
function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    
    const login = (userData) => {
        setUser(userData);
    };
    
    const logout = () => {
        setUser(null);
    };
    
    const value = {
        user,
        login,
        logout
    };
    
    return (
        <UserContext.Provider value={value}>
            {children}
        </UserContext.Provider>
    );
}

// 消费者组件
function Profile() {
    const { user } = useContext(UserContext);
    
    if (!user) {
        return <div>请先登录</div>;
    }
    
    return <div>欢迎，{user.name}！</div>;
}
```

### 2. Redux 基础

```jsx
// action types
export const INCREMENT = 'INCREMENT';
export const DECREMENT = 'DECREMENT';

// action creators
export const increment = () => ({ type: INCREMENT });
export const decrement = () => ({ type: DECREMENT });

// reducer
const initialState = { count: 0 };

export default function counterReducer(state = initialState, action) {
    switch (action.type) {
        case INCREMENT:
            return { count: state.count + 1 };
        case DECREMENT:
            return { count: state.count - 1 };
        default:
            return state;
    }
}

// store
import { createStore } from 'redux';
import counterReducer from './reducers';

const store = createStore(counterReducer);

// 使用Redux
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './actions';

function Counter() {
    const count = useSelector(state => state.count);
    const dispatch = useDispatch();
    
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => dispatch(increment())}>+</button>
            <button onClick={() => dispatch(decrement())}>-</button>
        </div>
    );
}
```

## React Router

### 1. 基本路由

```jsx
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom';

function App() {
    return (
        <Router>
            <div>
                <nav>
                    <ul>
                        <li>
                            <Link to="/">首页</Link>
                        </li>
                        <li>
                            <Link to="/about">关于</Link>
                        </li>
                        <li>
                            <Link to="/users">用户</Link>
                        </li>
                    </ul>
                </nav>

                {/* 路由配置 */}
                <Switch>
                    <Route path="/about">
                        <About />
                    </Route>
                    <Route path="/users">
                        <Users />
                    </Route>
                    <Route path="/">
                        <Home />
                    </Route>
                </Switch>
            </div>
        </Router>
    );
}
```

### 2. 动态路由

```jsx
// 路由配置
<Route path="/users/:id" component={UserDetail} />

// 组件中获取参数
function UserDetail({ match }) {
    const userId = match.params.id;
    return <div>用户ID：{userId}</div>;
}

// 使用useParams Hook
import { useParams } from 'react-router-dom';

function UserDetail() {
    const { id } = useParams();
    return <div>用户ID：{id}</div>;
}
```

## 性能优化

### 1. React.memo

```jsx
// 记忆组件，避免不必要的重渲染
const MemoizedComponent = React.memo(function MyComponent(props) {
    /* 使用props渲染 */
});
```

### 2. useCallback 和 useMemo

```jsx
// 使用useCallback缓存函数
const handleClick = useCallback(() => {
    console.log('Clicked!');
}, []);

// 使用useMemo缓存计算结果
const expensiveValue = useMemo(() => {
    // 昂贵的计算
    return computeExpensiveValue(a, b);
}, [a, b]);
```

### 3. 列表渲染优化

```jsx
// 为列表项添加唯一key
const items = data.map(item => (
    <li key={item.id}>
        {item.name}
    </li>
));
```

## 常见问题与解决方案

### 1. 避免直接修改状态

```javascript
// 错误
this.state.count = this.state.count + 1;

// 正确
this.setState({ count: this.state.count + 1 });

// 功能更新（推荐）
this.setState(prevState => ({
    count: prevState.count + 1
}));
```

### 2. 处理异步更新

```javascript
// setState是异步的
this.setState({ count: this.state.count + 1 });
console.log(this.state.count); // 不会立即更新

// 使用回调函数获取最新状态
this.setState({ count: this.state.count + 1 }, () => {
    console.log(this.state.count); // 会输出更新后的值
});
```

### 3. 事件处理中的this绑定

```javascript
// 方法1：构造函数中绑定
constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
}

// 方法2：箭头函数
handleClick = () => {
    console.log(this);
}

// 方法3：调用时绑定
<button onClick={() => this.handleClick()}>
```

@author wujingkai