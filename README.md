# 微服务部署指南

## 1. AWS EKS 部署

> 注意：本指南适用于 AWS CloudShell，已预装 Terraform、AWS CLI 和 kubectl。

### 1.1 部署基础设施

```bash
cd tf
terraform init
terraform plan
terraform apply
cd ..
```

## 2. 配置 kubectl

```bash
aws eks update-kubeconfig --region us-west-2 --name microservices-demo-cluster
```

## 3. 部署应用

```bash
# 安装 ALB Ingress Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/v2_5_4_full.yaml

# 部署所有服务
kubectl apply -f k8s/
```

## 4. 获取访问地址

```bash
# 获取 Ingress 地址（等待几分钟直到 ADDRESS 字段有值）
kubectl get ingress

# 示例输出：
# NAME                  CLASS   HOSTS   ADDRESS                                                                 PORTS   AGE
# microservices-ingress   alb     *       k8s-default-microservi-xxxxx-xxxxx.us-west-2.elb.amazonaws.com   80      5m
```

访问服务：
- 用户服务：`http://<ADDRESS>/users`
- 订单服务：`http://<ADDRESS>/orders`

## 5. 清理资源

```bash
# 删除 Kubernetes 资源
kubectl delete -f k8s/

# 销毁基础设施
cd tf
terraform destroy
```

## 2. Mac 本地部署

### 2.1 环境准备

```bash
# 安装 Homebrew（如果尚未安装）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装必要的工具
brew install kubectl

# 安装 minikube（两种方式任选其一）
# 方式一：使用 Homebrew（适用于 Intel Mac）
brew install minikube

# 方式二：手动安装（适用于 M1/M2 Mac）
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.35.0/minikube-darwin-arm64
sudo install minikube-darwin-arm64 /usr/local/bin/minikube

# 安装 Docker Desktop for Mac
brew install --cask docker
```

### 2.2 启动本地 Kubernetes 集群

```bash
# 启动 minikube
minikube start --driver=docker

# 启用 ingress 插件
minikube addons enable ingress
```

### 2.3 部署应用

```bash
# 部署所有服务
kubectl apply -f k8s/
```

### 2.4 获取访问地址

```bash
# 获取服务地址
minikube service --url user-service
```

访问服务：
- 用户服务：`<获取到的地址>/users`
- 订单服务：`<获取到的地址>/orders`

### 2.5 清理资源

```bash
# 删除 Kubernetes 资源
kubectl delete -f k8s/

# 停止 minikube
minikube stop
``` 