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

```bash
# 安装 ALB Ingress Controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/v2_5_4_full.yaml

# 部署所有服务
kubectl apply -f ../k8s/*.yaml
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
kubectl delete -f ../k8s/*.yaml

# 销毁基础设施
terraform destroy
``` 