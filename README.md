# Mac 微服务示例项目

## 环境准备

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

## 启动本地 Kubernetes 集群

```bash
# 启动 minikube
minikube start --driver=docker

# 启用 ingress 插件
minikube addons enable ingress
```

## 部署应用

```bash
# 部署所有服务
kubectl apply -f k8s/
```

## 获取访问地址

```bash
# 获取 ingress-nginx 控制器地址
minikube service -n ingress-nginx ingress-nginx-controller --url
```

访问服务：
- 用户服务：`http://<ingress-url>/users`
- 订单服务：`http://<ingress-url>/orders`

注意：
- 使用 `minikube service` 获取 ingress-nginx 控制器的地址
- 如果 `minikube service` 服务重启，需要重新获取地址

## 清理资源

```bash
# 删除 Kubernetes 资源
kubectl delete -f k8s/

# 停止 minikube
minikube stop
```

## 故障恢复演示

```bash
# 查看当前运行的 Pod
kubectl get pods

# 模拟服务故障（删除用户服务 Pod）
kubectl delete pod <user-service-pod-name>

# 观察新 Pod 自动创建
kubectl get pods -w

# 验证服务是否恢复
minikube service --url user-service
```

说明：
- 虽然本地环境只有一个节点，但 Kubernetes 仍然会保证服务的可用性
- 当 Pod 被删除后，Deployment 会自动创建新的 Pod 来替代
- 这个过程演示了 Kubernetes 的自我修复能力 