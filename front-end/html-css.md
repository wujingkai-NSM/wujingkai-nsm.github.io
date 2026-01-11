# HTML/CSS 基础知识

## HTML 基础

### 1. HTML 文档结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>页面标题</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>头部内容</header>
    <main>主要内容</main>
    <footer>底部内容</footer>
    <script src="script.js"></script>
</body>
</html>
```

### 2. 常用HTML标签

| 标签 | 描述 |
| :--- | :--- |
| `<div>` | 块级容器 |
| `<span>` | 行内容器 |
| `<a>` | 链接 |
| `<img>` | 图片 |
| `<ul>`/`<ol>`/`<li>` | 列表 |
| `<table>` | 表格 |
| `<form>` | 表单 |
| `<input>` | 输入框 |
| `<button>` | 按钮 |

## CSS 基础

### 1. CSS 选择器

| 选择器 | 描述 | 示例 |
| :--- | :--- | :--- |
| 元素选择器 | 选择指定元素 | `p { color: red; }` |
| ID选择器 | 选择指定ID的元素 | `#header { background: blue; }` |
| 类选择器 | 选择指定类的元素 | `.container { width: 1000px; }` |
| 后代选择器 | 选择后代元素 | `div p { margin: 10px; }` |
| 子选择器 | 选择直接子元素 | `div > p { font-size: 16px; }` |
| 相邻兄弟选择器 | 选择相邻兄弟元素 | `h1 + p { color: green; }` |
| 属性选择器 | 选择具有指定属性的元素 | `input[type="text"] { border: 1px solid #ccc; }` |
| 伪类选择器 | 选择元素的特定状态 | `a:hover { text-decoration: underline; }` |

### 2. CSS 盒模型

```
内容（content）→ 内边距（padding）→ 边框（border）→ 外边距（margin）
```

### 3. CSS 布局方式

- **流式布局**：元素宽度随容器变化
- **浮动布局**：使用`float`属性
- **定位布局**：使用`position`属性（static、relative、absolute、fixed、sticky）
- **Flex布局**：弹性盒模型，一维布局
- **Grid布局**：网格布局，二维布局

### 4. CSS 预处理器

- **Sass/SCSS**：功能强大的CSS预处理器
- **Less**：简洁的CSS预处理器
- **Stylus**：动态CSS预处理器

## 响应式设计

### 1. 媒体查询

```css
/* 移动端 */
@media (max-width: 767px) {
    .container {
        width: 100%;
    }
}

/* 平板 */
@media (min-width: 768px) and (max-width: 991px) {
    .container {
        width: 750px;
    }
}

/* 桌面端 */
@media (min-width: 992px) {
    .container {
        width: 970px;
    }
}
```

### 2. 响应式设计原则

- **移动优先**：先设计移动端，再扩展到桌面端
- **弹性布局**：使用相对单位（rem、em、%）
- **媒体查询**：针对不同屏幕尺寸优化
- **图片响应式**：使用`max-width: 100%`

## 常见问题与解决方案

### 1. 清除浮动

```css
.clearfix::after {
    content: "";
    display: table;
    clear: both;
}
```

### 2. 垂直居中

```css
/* Flex方式 */
.parent {
    display: flex;
    align-items: center;
    justify-content: center;
}

/* 绝对定位方式 */
.parent {
    position: relative;
}

.child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

### 3. 防止文本溢出

```css
.text-overflow {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}
```

@author wujingkai