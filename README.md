# AWS EKS 微服务部署指南

> 注意：本指南适用于 AWS CloudShell，已预装 Terraform、AWS CLI 和 kubectl。

## 1. 部署基础设施

```bash
cd tf
terraform init
terraform plan
terraform apply
```

## 2. 配置 kubectl

```bash
aws eks update-kubeconfig --region us-west-2 --name microservices-demo-cluster
```

## 3. 部署应用

### 3.1 先安装 Helm（CloudShell 环境）

AWS CloudShell 默认未安装 Helm，请先执行：

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
# 验证安装
helm version
```

### 3.2 安装 AWS Load Balancer Controller（ALB Ingress Controller）

```bash
# 添加 Helm 仓库并安装 Controller
helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=microservices-demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

### 3.3 部署所有服务

```bash
# 添加 database connection secret 到 k8s/secrets.yaml
kubectl apply -f ../k8s/
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
kubectl delete -f ../k8s/

# 销毁基础设施
terraform destroy
``` 

## 6. Debug

```
# cloudshell 存储空间低时须删除存储占用过大的目录
# 列出存储占用目录
du -sh ~/* ~/.??* 2>/dev/null | sort -hr | head -20

# 删除对应的repo即可
```