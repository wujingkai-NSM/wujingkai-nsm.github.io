# Python 机器学习库用法

## 1. NumPy - 数值计算基础

### 1.1 简介
NumPy（Numerical Python）是 Python 科学计算的基础库，提供高效的多维数组对象和丰富的数值计算函数。

### 1.2 核心功能
- 多维数组（ndarray）
- 广播功能
- 线性代数运算
- 傅里叶变换
- 随机数生成

### 1.3 常用操作

#### 1.3.1 数组创建
```python
import numpy as np

# 创建一维数组
a = np.array([1, 2, 3, 4, 5])

# 创建二维数组
b = np.array([[1, 2, 3], [4, 5, 6]])

# 创建特殊数组
zeros = np.zeros((2, 3))  # 全零数组
ones = np.ones((2, 3))    # 全一数组
full = np.full((2, 3), 5) # 填充指定值
arange = np.arange(10)    # 类似 range，返回数组
linspace = np.linspace(0, 1, 5) # 等间隔数组
```

#### 1.3.2 数组属性
```python
print(b.shape)    # 数组形状：(2, 3)
print(b.ndim)     # 数组维度：2
print(b.size)     # 数组元素个数：6
print(b.dtype)    # 数组数据类型：int64
```

#### 1.3.3 数组索引与切片
```python
# 一维数组索引
a[0]     # 第一个元素

a[1:4]   # 切片：[2, 3, 4]

a[::2]   # 步长为2：[1, 3, 5]

# 二维数组索引
b[0, 1]  # 第一行第二列：2

b[:, 0]  # 第一列：[1, 4]

b[1, :]  # 第二行：[4, 5, 6]
```

#### 1.3.4 数组运算
```python
# 基本运算
c = a + 2  # 所有元素加2

d = a * 3  # 所有元素乘3

# 矩阵乘法
e = np.dot(b, b.T)  # 矩阵乘法

# 统计运算
print(np.mean(a))   # 平均值
print(np.median(a)) # 中位数
print(np.std(a))    # 标准差
print(np.max(a))    # 最大值
print(np.min(a))    # 最小值
```

## 2. Pandas - 数据处理与分析

### 2.1 简介
Pandas 是 Python 数据分析库，提供高效的数据结构（Series 和 DataFrame）和数据分析工具。

### 2.2 核心数据结构

#### 2.2.1 Series
一维带标签的数组
```python
import pandas as pd

# 创建 Series
s = pd.Series([1, 3, 5, np.nan, 6, 8], index=['a', 'b', 'c', 'd', 'e', 'f'])
```

#### 2.2.2 DataFrame
二维带标签的数据结构，类似表格
```python
# 从字典创建 DataFrame
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['New York', 'London', 'Paris']
}
df = pd.DataFrame(data)

# 从 CSV 文件读取
df = pd.read_csv('data.csv')

# 从 Excel 文件读取
df = pd.read_excel('data.xlsx')
```

### 2.3 常用操作

#### 2.3.1 数据查看
```python
print(df.head())      # 查看前5行
print(df.tail())      # 查看后5行
print(df.info())      # 查看数据信息
print(df.describe())  # 查看统计描述
print(df.columns)     # 查看列名
print(df.index)       # 查看索引
```

#### 2.3.2 数据选择
```python
# 选择列
df['name']            # 单列

df[['name', 'age']]   # 多列

# 选择行
df.loc[0]             # 按标签选择

df.iloc[0]            # 按位置选择

df.loc[df['age'] > 30] # 条件选择
```

#### 2.3.3 数据清洗
```python
# 处理缺失值
df.dropna()           # 删除缺失值
df.fillna(0)          # 填充缺失值
df.fillna(df.mean())  # 用平均值填充

# 处理重复值
df.drop_duplicates()  # 删除重复行

# 数据类型转换
df['age'] = df['age'].astype(str)  # 转换为字符串

# 重命名列
df.rename(columns={'name': 'full_name'}, inplace=True)
```

#### 2.3.4 数据处理
```python
# 添加新列
df['salary'] = [50000, 60000, 70000]

# 应用函数
df['age_in_10_years'] = df['age'].apply(lambda x: x + 10)

# 分组统计
df.groupby('city')['age'].mean()  # 按城市分组计算平均年龄

# 合并数据
left = pd.DataFrame({'key': ['k0', 'k1'], 'value': ['v0', 'v1']})
right = pd.DataFrame({'key': ['k0', 'k1'], 'value': ['v2', 'v3']})
merged = pd.merge(left, right, on='key', suffixes=('_left', '_right'))

# 排序
df.sort_values(by='age', ascending=False)
```

#### 2.3.5 数据导出
```python
# 导出到 CSV
df.to_csv('output.csv', index=False)

# 导出到 Excel
df.to_excel('output.xlsx', index=False)
```

## 3. Matplotlib/Seaborn - 数据可视化

### 3.1 Matplotlib 简介
Matplotlib 是 Python 绘图库，提供灵活的绘图功能，支持多种图表类型。

### 3.2 常用图表

#### 3.2.1 折线图
```python
import matplotlib.pyplot as plt

# 数据
x = np.linspace(0, 10, 100)
y = np.sin(x)

# 创建图表
plt.figure(figsize=(10, 6))
plt.plot(x, y, label='sin(x)')
plt.title('正弦函数')
plt.xlabel('x')
plt.ylabel('y')
plt.legend()
plt.grid(True)
plt.show()
```

#### 3.2.2 散点图
```python
# 数据
x = np.random.rand(100)
y = np.random.rand(100)
colors = np.random.rand(100)
sizes = 1000 * np.random.rand(100)

# 创建散点图
plt.scatter(x, y, c=colors, s=sizes, alpha=0.5, cmap='viridis')
plt.colorbar()  # 添加颜色条
plt.show()
```

#### 3.2.3 直方图
```python
# 数据
data = np.random.randn(1000)

# 创建直方图
plt.hist(data, bins=30, alpha=0.7, edgecolor='black')
plt.title('直方图')
plt.xlabel('值')
plt.ylabel('频率')
plt.show()
```

### 3.3 Seaborn 简介
Seaborn 是基于 Matplotlib 的高级绘图库，提供更美观的图表和更简单的 API。

### 3.4 Seaborn 常用图表

#### 3.4.1 箱线图
```python
import seaborn as sns

# 加载示例数据
tips = sns.load_dataset('tips')

# 创建箱线图
sns.boxplot(x='day', y='total_bill', data=tips)
sns.swarmplot(x='day', y='total_bill', data=tips, color='.25')
plt.show()
```

#### 3.4.2 热力图
```python
# 创建相关系数矩阵
corr = df.corr()

# 创建热力图
plt.figure(figsize=(10, 8))
sns.heatmap(corr, annot=True, cmap='coolwarm', fmt='.2f')
plt.title('相关系数热力图')
plt.show()
```

## 4. Scikit-learn - 机器学习算法库

### 4.1 简介
Scikit-learn 是 Python 最流行的机器学习库，提供了丰富的算法和工具，包括分类、回归、聚类、降维等。

### 4.2 核心功能
- 数据预处理
- 模型选择与评估
- 监督学习算法
- 无监督学习算法
- 特征工程
- 模型持久化

### 4.3 常用模块

#### 4.3.1 数据预处理
```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, OneHotEncoder

# 标准化
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# 归一化
minmax_scaler = MinMaxScaler()
X_minmax = minmax_scaler.fit_transform(X)

# 独热编码
encoder = OneHotEncoder()
categorical_features = encoder.fit_transform(categorical_data)
```

#### 4.3.2 模型训练与预测
```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error

# 划分数据集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 初始化模型
model = LinearRegression()

# 训练模型
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
mse = mean_squared_error(y_test, y_pred)
rmse = np.sqrt(mse)
```

#### 4.3.3 模型选择
```python
from sklearn.model_selection import cross_val_score, GridSearchCV
from sklearn.ensemble import RandomForestRegressor

# 交叉验证
scores = cross_val_score(RandomForestRegressor(), X, y, cv=5, scoring='neg_mean_squared_error')

# 网格搜索
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 10, 20]
}

grid_search = GridSearchCV(RandomForestRegressor(), param_grid, cv=5, scoring='neg_mean_squared_error')
grid_search.fit(X_train, y_train)

best_model = grid_search.best_estimator_
```

## 5. TensorFlow/PyTorch - 深度学习框架

### 5.1 TensorFlow 简介
TensorFlow 是 Google 开发的开源深度学习框架，广泛应用于各种深度学习任务。

### 5.2 TensorFlow 基本操作
```python
import tensorflow as tf

# 创建张量
x = tf.constant([1, 2, 3])
y = tf.constant([4, 5, 6])

# 张量运算
z = x + y

# 创建模型
model = tf.keras.Sequential([
    tf.keras.layers.Dense(64, activation='relu', input_shape=(input_dim,)),
    tf.keras.layers.Dense(32, activation='relu'),
    tf.keras.layers.Dense(output_dim, activation='softmax')
])

# 编译模型
model.compile(optimizer='adam',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# 训练模型
model.fit(X_train, y_train, epochs=10, batch_size=32, validation_data=(X_val, y_val))

# 评估模型
loss, accuracy = model.evaluate(X_test, y_test)
```

### 5.3 PyTorch 简介
PyTorch 是 Facebook 开发的开源深度学习框架，以动态计算图和易用性著称。

### 5.4 PyTorch 基本操作
```python
import torch
import torch.nn as nn
import torch.optim as optim

# 创建张量
x = torch.tensor([1, 2, 3])
y = torch.tensor([4, 5, 6])

# 张量运算
z = x + y

# 定义模型
class NeuralNetwork(nn.Module):
    def __init__(self, input_dim, output_dim):
        super(NeuralNetwork, self).__init__()
        self.fc1 = nn.Linear(input_dim, 64)
        self.fc2 = nn.Linear(64, 32)
        self.fc3 = nn.Linear(32, output_dim)
        self.relu = nn.ReLU()
    
    def forward(self, x):
        x = self.relu(self.fc1(x))
        x = self.relu(self.fc2(x))
        x = self.fc3(x)
        return x

# 初始化模型
model = NeuralNetwork(input_dim, output_dim)

# 定义损失函数和优化器
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=0.001)

# 训练模型
for epoch in range(10):
    # 前向传播
    outputs = model(X_train)
    loss = criterion(outputs, y_train)
    
    # 反向传播和优化
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    
    print(f'Epoch {epoch+1}, Loss: {loss.item():.4f}')
```

## 6. 最佳实践

### 6.1 库的安装
```bash
# 安装基础库
pip install numpy pandas matplotlib seaborn scikit-learn

# 安装深度学习框架
pip install tensorflow
pip install torch torchvision torchaudio
```

### 6.2 代码组织
- 导入库的顺序：标准库 → 第三方库 → 自定义库
- 使用别名：numpy as np, pandas as pd, matplotlib.pyplot as plt, seaborn as sns
- 保持代码简洁，避免重复

### 6.3 性能优化
- 尽量使用向量化操作，避免显式循环
- 合理使用数据类型，减少内存占用
- 对于大规模数据，考虑使用 Dask 或 Vaex 等库

### 6.4 可视化最佳实践
- 选择合适的图表类型
- 保持图表简洁明了
- 添加标题、标签和图例
- 使用合适的颜色方案
- 考虑图表的可读性

## 7. 总结
Python 提供了丰富的机器学习库，从基础的数值计算到高级的深度学习框架，满足不同层次的需求。熟练掌握这些库的使用，对于进行机器学习项目至关重要。

建议学习顺序：
1. NumPy → 2. Pandas → 3. Matplotlib/Seaborn → 4. Scikit-learn → 5. TensorFlow/PyTorch

通过不断实践和项目经验，逐步提高对这些库的理解和应用能力。

@author wujingkai