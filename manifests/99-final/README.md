## 最终部署

### 部署redis
```
# 跟04-statefulset/redis.yaml一致
kubectl apply -f redis.yaml
```

### 部署openresty
```
# 结合02-deployment/openresty-deployment-replicas10-configmap.yaml 和 03-service/openresty-service-loadbalancer.yaml
# 添加 openresty 操作redis
kubectl apply -f openresty.yaml
# 模拟下， 直接设置externalIPs (此处your_ip为服务器IP地址)
kubectl patch service openresty -p '{"spec":{"externalIPs":["your_ip"]}}'

curl your_ip/count # 这里会返回服务端地址（Pod地址），地址会变化， 同时也有一个自增的Count
# 检查redis中数字
kubectl exec -it redis-0 -- redis-cli get count

kubectl delete -f openresty.yaml
kubectl delete -f redis.yaml
kubectl delete pvc data-redis-0
```
