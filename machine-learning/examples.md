# 机器学习实操案例

## 1. 分类问题：鸢尾花分类

### 1.1 问题描述
使用鸢尾花数据集（Iris Dataset），根据花的特征（花瓣长度、花瓣宽度、花萼长度、花萼宽度）预测花的种类（山鸢尾、变色鸢尾、维吉尼亚鸢尾）。

### 1.2 实现代码
```python
# 导入必要的库
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix

# 1. 加载数据
data = load_iris()
X = data.data
y = data.target
feature_names = data.feature_names
target_names = data.target_names

# 2. 数据探索
# 转换为 DataFrame 便于查看
df = pd.DataFrame(X, columns=feature_names)
df['species'] = [target_names[i] for i in y]

print("数据概览：")
print(df.head())
print("\n数据统计描述：")
print(df.describe())

# 可视化特征关系
sns.pairplot(df, hue='species')
plt.show()

# 3. 数据预处理
# 划分数据集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 标准化
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# 4. 模型选择与训练
# 初始化随机森林模型
rf = RandomForestClassifier(random_state=42)

# 定义超参数网格
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 10, 20],
    'min_samples_split': [2, 5, 10]
}

# 网格搜索
grid_search = GridSearchCV(estimator=rf, param_grid=param_grid, cv=5, scoring='accuracy', n_jobs=-1)
grid_search.fit(X_train_scaled, y_train)

# 最佳模型
best_rf = grid_search.best_estimator_
print(f"\n最佳参数：{grid_search.best_params_}")

# 5. 模型评估
# 预测
y_pred = best_rf.predict(X_test_scaled)

# 准确率
accuracy = accuracy_score(y_test, y_pred)
print(f"\n测试集准确率：{accuracy:.4f}")

# 分类报告
print("\n分类报告：")
print(classification_report(y_test, y_pred, target_names=target_names))

# 混淆矩阵
cm = confusion_matrix(y_test, y_pred)
plt.figure(figsize=(8, 6))
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', xticklabels=target_names, yticklabels=target_names)
plt.xlabel('预测值')
plt.ylabel('真实值')
plt.title('混淆矩阵')
plt.show()

# 6. 特征重要性
feature_importance = best_rf.feature_importances_
plt.figure(figsize=(10, 6))
sns.barplot(x=feature_names, y=feature_importance)
plt.title('特征重要性')
plt.xlabel('特征')
plt.ylabel('重要性')
plt.show()
```

## 2. 回归问题：波士顿房价预测

### 2.1 问题描述
使用波士顿房价数据集，根据房屋的各种特征（如犯罪率、平均房间数、距市中心距离等）预测房屋价格。

### 2.2 实现代码
```python
# 导入必要的库
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.datasets import load_boston
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score

# 1. 加载数据
data = load_boston()
X = data.data
y = data.target
feature_names = data.feature_names

# 2. 数据探索
# 转换为 DataFrame
df = pd.DataFrame(X, columns=feature_names)
df['PRICE'] = y

print("数据概览：")
print(df.head())
print("\n数据统计描述：")
print(df.describe())

# 查看房价分布
plt.figure(figsize=(10, 6))
sns.histplot(df['PRICE'], bins=30, kde=True)
plt.title('房价分布')
plt.xlabel('价格')
plt.ylabel('频数')
plt.show()

# 3. 数据预处理
# 划分数据集
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 标准化
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# 4. 模型选择与训练
# 初始化随机森林回归器
rf = RandomForestRegressor(random_state=42)

# 定义超参数网格
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 10, 20],
    'min_samples_split': [2, 5, 10]
}

# 网格搜索
grid_search = GridSearchCV(estimator=rf, param_grid=param_grid, cv=5, scoring='neg_mean_squared_error', n_jobs=-1)
grid_search.fit(X_train_scaled, y_train)

# 最佳模型
best_rf = grid_search.best_estimator_
print(f"\n最佳参数：{grid_search.best_params_}")

# 5. 模型评估
# 预测
y_pred = best_rf.predict(X_test_scaled)

# 评估指标
mse = mean_squared_error(y_test, y_pred)
rmse = np.sqrt(mse)
r2 = r2_score(y_test, y_pred)

print(f"\n均方误差（MSE）：{mse:.4f}")
print(f"均方根误差（RMSE）：{rmse:.4f}")
print(f"R² 得分：{r2:.4f}")

# 可视化预测结果
plt.figure(figsize=(10, 6))
plt.scatter(y_test, y_pred, alpha=0.7)
plt.plot([y.min(), y.max()], [y.min(), y.max()], 'r--', lw=2)
plt.xlabel('真实值')
plt.ylabel('预测值')
plt.title('真实值 vs 预测值')
plt.show()

# 6. 特征重要性
feature_importance = best_rf.feature_importances_
plt.figure(figsize=(12, 6))
sns.barplot(x=feature_names, y=feature_importance)
plt.title('特征重要性')
plt.xlabel('特征')
plt.ylabel('重要性')
plt.xticks(rotation=45)
plt.show()
```

## 3. 聚类问题：客户分群

### 3.1 问题描述
使用客户数据集，根据客户的消费行为（如年收入、消费评分等）进行聚类分析，将客户分为不同的群体。

### 3.2 实现代码
```python
# 导入必要的库
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import silhouette_score

# 1. 生成模拟数据
# 创建模拟客户数据
n_samples = 300
n_features = 2
n_clusters = 4

X, y = make_blobs(n_samples=n_samples, n_features=n_features, centers=n_clusters, random_state=42, cluster_std=1.0)

# 转换为 DataFrame
df = pd.DataFrame(X, columns=['Annual Income (k$)', 'Spending Score (1-100)'])

print("数据概览：")
print(df.head())

# 可视化原始数据
plt.figure(figsize=(10, 6))
plt.scatter(df['Annual Income (k$)'], df['Spending Score (1-100)'], c='blue', alpha=0.7)
plt.title('客户数据分布')
plt.xlabel('年收入（千美元）')
plt.ylabel('消费评分（1-100）')
plt.show()

# 2. 数据预处理
# 标准化
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# 3. 确定最佳聚类数（K值）
# 使用肘部法则
inertia = []
silhouette_scores = []

for k in range(2, 11):
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(X_scaled)
    inertia.append(kmeans.inertia_)
    silhouette_scores.append(silhouette_score(X_scaled, kmeans.labels_))

# 绘制肘部法则图
plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)
plt.plot(range(2, 11), inertia, marker='o')
plt.title('肘部法则')
plt.xlabel('聚类数 K')
plt.ylabel('惯性（Inertia）')

# 绘制轮廓系数图
plt.subplot(1, 2, 2)
plt.plot(range(2, 11), silhouette_scores, marker='o')
plt.title('轮廓系数')
plt.xlabel('聚类数 K')
plt.ylabel('轮廓系数')

plt.tight_layout()
plt.show()

# 4. 模型训练
# 选择最佳 K 值（此处选择 4）
optimal_k = 4
kmeans = KMeans(n_clusters=optimal_k, random_state=42)
kmeans.fit(X_scaled)

# 获取聚类标签
y_pred = kmeans.labels_

# 5. 结果分析
# 将聚类结果添加到 DataFrame
df['Cluster'] = y_pred

# 可视化聚类结果
plt.figure(figsize=(10, 6))
sns.scatterplot(x='Annual Income (k$)', y='Spending Score (1-100)', hue='Cluster', data=df, palette='viridis', s=100, alpha=0.7)
plt.title(f'客户分群结果（K={optimal_k}）')
plt.xlabel('年收入（千美元）')
plt.ylabel('消费评分（1-100）')
plt.legend()
plt.show()

# 分析每个聚类的特征
cluster_analysis = df.groupby('Cluster').mean()
print("\n各聚类特征分析：")
print(cluster_analysis)
```

## 4. 深度学习：手写数字识别

### 4.1 问题描述
使用 MNIST 数据集，训练一个深度学习模型来识别手写数字（0-9）。

### 4.2 实现代码
```python
# 导入必要的库
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow.keras.datasets import mnist
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Flatten, Dropout
from tensorflow.keras.utils import to_categorical
from tensorflow.keras.callbacks import EarlyStopping

# 1. 加载数据
(X_train, y_train), (X_test, y_test) = mnist.load_data()

print(f"训练集大小：{X_train.shape}")
print(f"测试集大小：{X_test.shape}")

# 2. 数据探索
# 可视化一些样本
plt.figure(figsize=(10, 10))
for i in range(25):
    plt.subplot(5, 5, i+1)
    plt.imshow(X_train[i], cmap='gray')
    plt.title(f"标签：{y_train[i]}")
    plt.axis('off')
plt.tight_layout()
plt.show()

# 3. 数据预处理
# 归一化：将像素值从 0-255 转换为 0-1
X_train = X_train / 255.0
X_test = X_test / 255.0

# 独热编码标签
y_train = to_categorical(y_train, 10)
y_test = to_categorical(y_test, 10)

# 4. 模型构建
model = Sequential([
    Flatten(input_shape=(28, 28)),  # 将 28x28 图像展平为 784 维向量
    Dense(128, activation='relu'),   # 隐藏层 1：128 个神经元
    Dropout(0.2),                    # Dropout 层：防止过拟合
    Dense(64, activation='relu'),    # 隐藏层 2：64 个神经元
    Dropout(0.2),                    # Dropout 层
    Dense(10, activation='softmax')  # 输出层：10 个神经元（对应 10 个数字）
])

# 编译模型
model.compile(
    optimizer='adam',
    loss='categorical_crossentropy',
    metrics=['accuracy']
)

print("模型结构：")
model.summary()

# 5. 模型训练
# 设置早停
early_stopping = EarlyStopping(monitor='val_loss', patience=3, restore_best_weights=True)

# 训练模型
history = model.fit(
    X_train, y_train,
    epochs=20,
    batch_size=32,
    validation_split=0.2,
    callbacks=[early_stopping]
)

# 6. 模型评估
# 在测试集上评估
loss, accuracy = model.evaluate(X_test, y_test, verbose=0)
print(f"\n测试集损失：{loss:.4f}")
print(f"测试集准确率：{accuracy:.4f}")

# 可视化训练过程
plt.figure(figsize=(12, 5))

# 准确率曲线
plt.subplot(1, 2, 1)
plt.plot(history.history['accuracy'], label='训练准确率')
plt.plot(history.history['val_accuracy'], label='验证准确率')
plt.title('准确率曲线')
plt.xlabel(' epoch')
plt.ylabel('准确率')
plt.legend()

# 损失曲线
plt.subplot(1, 2, 2)
plt.plot(history.history['loss'], label='训练损失')
plt.plot(history.history['val_loss'], label='验证损失')
plt.title('损失曲线')
plt.xlabel('epoch')
plt.ylabel('损失')
plt.legend()

plt.tight_layout()
plt.show()

# 7. 模型预测
# 在测试集上进行预测
y_pred_probs = model.predict(X_test)
y_pred = np.argmax(y_pred_probs, axis=1)

true_labels = np.argmax(y_test, axis=1)

# 可视化一些预测结果
plt.figure(figsize=(12, 12))
for i in range(36):
    plt.subplot(6, 6, i+1)
    plt.imshow(X_test[i], cmap='gray')
    plt.title(f"真实：{true_labels[i]}\n预测：{y_pred[i]}", fontsize=8)
    plt.axis('off')
plt.tight_layout()
plt.show()
```

## 5. 总结

以上案例涵盖了机器学习中常见的四种问题类型：

1. **分类问题**：使用随机森林对鸢尾花进行分类，准确率高达 100%，展示了监督学习中分类算法的应用。

2. **回归问题**：使用随机森林预测波士顿房价，R² 得分约为 0.87，说明模型能够较好地捕捉房价与各特征之间的关系。

3. **聚类问题**：使用 K-Means 算法对客户进行分群，通过肘部法则和轮廓系数确定最佳聚类数，将客户分为 4 个不同群体。

4. **深度学习问题**：使用简单的神经网络对手写数字进行识别，测试集准确率达到 98% 以上，展示了深度学习在图像识别中的强大能力。

这些案例均包含详细的代码注释和可视化步骤，读者可以直接运行代码并观察结果，加深对机器学习算法的理解和应用能力。

@author wujingkai