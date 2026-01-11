# DevOps 核心知识

## Docker

### 1. 基本概念

| 概念 | 描述 |
| :--- | :--- |
| **镜像（Image）** | 只读模板，包含运行应用所需的所有内容 |
| **容器（Container）** | 镜像的可运行实例，包含应用及其运行环境 |
| **仓库（Repository）** | 存储镜像的地方，如Docker Hub |
| **Dockerfile** | 定义如何构建Docker镜像的文本文件 |
| **Docker Compose** | 用于定义和运行多容器Docker应用的工具 |

### 2. Dockerfile基础

```dockerfile
# 指定基础镜像
FROM openjdk:11-jre-slim

# 设置工作目录
WORKDIR /app

# 复制文件到容器
COPY target/myapp.jar app.jar

# 暴露端口
EXPOSE 8080

# 定义环境变量
ENV SPRING_PROFILES_ACTIVE=prod

# 运行命令
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 3. 常用Docker命令

```bash
# 构建镜像
docker build -t myapp:1.0 .

# 查看本地镜像
docker images

# 运行容器
docker run -d -p 8080:8080 --name myapp-container myapp:1.0

# 查看运行中的容器
docker ps

# 查看所有容器
docker ps -a

# 停止容器
docker stop myapp-container

# 启动容器
docker start myapp-container

# 删除容器
docker rm myapp-container

# 删除镜像
docker rmi myapp:1.0

# 进入容器
docker exec -it myapp-container bash

# 查看容器日志
docker logs myapp-container
# 实时查看日志
docker logs -f myapp-container

# 推送镜像到仓库
docker push username/myapp:1.0

# 拉取镜像
docker pull username/myapp:1.0
```

### 4. Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=prod
      - DB_URL=jdbc:mysql://db:3306/mydb
      - DB_USERNAME=root
      - DB_PASSWORD=password
    depends_on:
      - db
    restart: always

  db:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=mydb
    volumes:
      - mysql-data:/var/lib/mysql
    restart: always

volumes:
  mysql-data:
```

```bash
# 启动所有服务
docker-compose up -d

# 停止所有服务
docker-compose down

# 查看服务状态
docker-compose ps

# 查看服务日志
docker-compose logs
# 查看特定服务日志
docker-compose logs app

# 构建并启动服务
docker-compose up -d --build

# 进入服务容器
docker-compose exec app bash
```

## Kubernetes (K8s)

### 1. 基本概念

| 概念 | 描述 |
| :--- | :--- |
| **Pod** | K8s的最小部署单元，包含一个或多个容器 |
| **Deployment** | 管理Pod的副本，确保指定数量的Pod运行 |
| **Service** | 为Pod提供稳定的网络访问方式 |
| **Ingress** | 管理外部访问集群内服务的规则 |
| **ConfigMap** | 存储配置数据，与Pod分离 |
| **Secret** | 存储敏感数据，如密码、密钥等 |
| **Volume** | 为Pod提供持久化存储 |
| **Namespace** | 将集群划分为多个虚拟集群 |
| **Label** | 用于标识和选择资源的键值对 |
| **Annotation** | 为资源添加非标识性元数据 |

### 2. 核心资源配置

#### 2.1 Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:1.0
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "prod"
        - name: DB_URL
          valueFrom:
            configMapKeyRef:
              name: myapp-config
              key: db-url
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: myapp-secret
              key: db-password
```

#### 2.2 Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer  # 或ClusterIP、NodePort
```

#### 2.3 ConfigMap和Secret

```yaml
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  db-url: jdbc:mysql://mysql-service:3306/mydb
  db-username: root

# Secret
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret
type: Opaque
data:
  db-password: cGFzc3dvcmQ=  # base64编码的"password"
```

#### 2.4 Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

### 3. 常用kubectl命令

```bash
# 查看所有资源
kubectl get all

# 查看Pod
kubectl get pods
kubectl get pods -n my-namespace  # 指定命名空间

# 查看Deployment
kubectl get deployments

# 查看Service
kubectl get services

# 查看ConfigMap
kubectl get configmaps

# 查看Secret
kubectl get secrets

# 查看命名空间
kubectl get namespaces

# 创建资源
kubectl apply -f deployment.yaml

# 删除资源
kubectl delete -f deployment.yaml

# 进入Pod
kubectl exec -it myapp-pod -- bash

# 查看Pod日志
kubectl logs myapp-pod
kubectl logs -f myapp-pod  # 实时查看

# 查看Pod详细信息
kubectl describe pod myapp-pod

# 缩放Deployment
kubectl scale deployment myapp-deployment --replicas=5

# 滚动更新
kubectl set image deployment myapp-deployment myapp=myapp:2.0

# 查看滚动更新状态
kubectl rollout status deployment myapp-deployment

# 回滚更新
kubectl rollout undo deployment myapp-deployment
```

## CI/CD

### 1. CI/CD概念

- **CI（持续集成）**：频繁地将代码集成到主干，每次集成都运行自动化测试
- **CD（持续交付/部署）**：将集成后的代码自动部署到测试环境或生产环境

### 2. GitHub Actions示例

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'adopt'
    
    - name: Build with Maven
      run: mvn clean package -DskipTests
    
    - name: Run tests
      run: mvn test
    
    - name: Build and push Docker image
      if: github.event_name == 'push' && github.ref == 'refs/heads/main'
      uses: docker/build-push-action@v4
      with:
        context: .
        push: true
        tags: ${{ secrets.DOCKER_HUB_USERNAME }}/myapp:${{ github.sha }},${{ secrets.DOCKER_HUB_USERNAME }}/myapp:latest
      env:
        DOCKER_BUILDKIT: 1
        DOCKER_USERNAME: ${{ secrets.DOCKER_HUB_USERNAME }}
        DOCKER_PASSWORD: ${{ secrets.DOCKER_HUB_TOKEN }}
    
    - name: Deploy to Kubernetes
      if: github.event_name == 'push' && github.ref == 'refs/heads/main'
      uses: azure/k8s-deploy@v4
      with:
        namespace: myapp
        manifests: |
          k8s/deployment.yaml
          k8s/service.yaml
        images: |
          ${{ secrets.DOCKER_HUB_USERNAME }}/myapp:${{ github.sha }}
        kubectl-version: 'latest'
      env:
        KUBECONFIG: ${{ secrets.KUBECONFIG }}
```

### 3. Jenkins Pipeline示例

```groovy
pipeline {
    agent any
    
    tools {
        maven 'Maven 3.8.5'
        jdk 'Java 11'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/username/myapp.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Docker Build') {
            when {
                branch 'main'
            }
            steps {
                sh 'docker build -t myapp:${BUILD_NUMBER} .'
                sh 'docker tag myapp:${BUILD_NUMBER} myapp:latest'
            }
        }
        
        stage('Docker Push') {
            when {
                branch 'main'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh 'docker push myapp:${BUILD_NUMBER}'
                    sh 'docker push myapp:latest'
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'export KUBECONFIG=$KUBECONFIG'
                    sh 'kubectl set image deployment/myapp-deployment myapp=myapp:${BUILD_NUMBER} -n myapp'
                    sh 'kubectl rollout status deployment/myapp-deployment -n myapp'
                }
            }
        }
    }
    
    post {
        success {
            slackSend channel: '#ci-cd', message: 'Build ${BUILD_NUMBER} succeeded!'
        }
        failure {
            slackSend channel: '#ci-cd', message: 'Build ${BUILD_NUMBER} failed!'
        }
    }
}
```

## 监控与日志

### 1. Prometheus + Grafana

- **Prometheus**：开源监控系统，用于收集和存储时间序列数据
- **Grafana**：开源数据可视化工具，用于展示Prometheus收集的数据

```yaml
# Prometheus配置示例
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
    - role: pod
    relabel_configs:
    - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
      action: keep
      regex: true
    - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
      action: replace
      target_label: __metrics_path__
      regex: (.+)
    - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
      action: replace
      regex: ([^:]+)(?::\d+)?;(\d+)
      replacement: $1:$2
      target_label: __address__
```

### 2. ELK Stack

- **Elasticsearch**：分布式搜索引擎，用于存储日志数据
- **Logstash**：日志收集和处理工具
- **Kibana**：日志可视化工具

### 3. EFK Stack

- **Elasticsearch**：分布式搜索引擎
- **Fluentd**：日志收集和处理工具，替代Logstash
- **Kibana**：日志可视化工具

## 基础设施即代码 (IaC)

### 1. Terraform

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "web-server"
  }
}

resource "aws_security_group" "web-sg" {
  name        = "web-sg"
  description = "Allow HTTP traffic"
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

```bash
# 初始化Terraform
terraform init

# 预览变更
terraform plan

# 应用变更
terraform apply

# 销毁资源
terraform destroy
```

### 2. Ansible

```yaml
# playbook.yml
- name: Configure web servers
  hosts: webservers
  become: yes
  
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Copy Nginx configuration
      copy:
        src: files/nginx.conf
        dest: /etc/nginx/nginx.conf
      notify:
        - Restart Nginx
    
    - name: Start Nginx service
      service:
        name: nginx
        state: started
        enabled: yes
  
  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

```bash
# 运行Playbook
ansible-playbook -i inventory.ini playbook.yml

# 查看主机列表
ansible all -i inventory.ini --list-hosts

# 运行临时命令
ansible webservers -i inventory.ini -m ping
ansible webservers -i inventory.ini -m command -a "uptime"
```

## 监控与告警

### 1. 监控指标

| 类别 | 指标 |
| :--- | :--- |
| **系统指标** | CPU使用率、内存使用率、磁盘空间、网络流量 |
| **应用指标** | 响应时间、请求量、错误率、吞吐量 |
| **数据库指标** | 查询响应时间、连接数、缓存命中率、锁等待时间 |
| **容器指标** | 容器CPU、内存、网络、存储使用率 |
| **K8s指标** | Pod状态、节点状态、资源使用率、API服务器指标 |

### 2. 告警策略

- **告警级别**：
  - 严重（Critical）：系统不可用，需要立即处理
  - 警告（Warning）：系统性能下降，需要关注
  - 信息（Info）：正常运行信息

- **告警渠道**：
  - 邮件
  - 短信
  - 即时通讯工具（Slack、钉钉、企业微信）
  - 电话

@author wujingkai