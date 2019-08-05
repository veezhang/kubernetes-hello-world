## Deployment使用
### 部署Deployment
```
kubectl apply -f openresty-deployment.yaml
# 查看生成了哪些资源 (Deployment, Replicaset, Pod)
kubectl get all
# 试试删除Pod, Replicaset, Deployment 会怎么样
kubectl delete pod openresty-xxxxx-xxx
kubectl get all
kubectl delete replicaset openresty-xxxxx
kubectl get all
kubectl delete deployment openresty
kubectl get all
```

### 部署Deployment（修改replicas）
```
# 试着修改replicas为10试试
kubectl apply -f openresty-deployment-replicas10.yaml
# 查看生成了哪些资源 (Deployment, Replicaset, Pod)， Pod变为10个了
kubectl get all
# 在本机访问每个Pod的试试看
kubectl get pod -owide
curl your_ip

# scale replicas
kubectl scale deployment openresty --replicas=5
kubectl get all

kubectl delete -f openresty-deployment-replicas10.yaml
```

### 部署Deployment（修改replicas+configmap）
```
kubectl apply -f openresty-deployment-replicas10-configmap.yaml
# 在本机访问每个Pod的试试看
kubectl get pod -owide
curl your_ip/hello-world.html
curl your_ip/hello-world # 这里会返回服务端地址（Pod地址）

kubectl delete -f openresty-deployment-replicas10-configmap.yaml

# 怎么负载均衡多个Pod？？
```
