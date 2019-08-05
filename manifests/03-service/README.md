## Service使用
### 前置部署
```
# 在deployment目录下部署openresty
kubectl apply -f ../02-deployment/openresty-deployment-replicas10-configmap.yaml
# 在pod目录下面部署busybox（工具命令集合）
kubectl apply -f ../01-pod/busybox-pod.yaml
```

### 部署Service （ClusterIP默认值）
```
kubectl apply -f openresty-service.yaml
# 获取service
kubectl get service openresty -owide
kubectl get endpoints openresty -owide #-oyaml

kubectl exec -it busybox -- wget -q -O - openresty/hello-world.html
kubectl exec -it busybox -- wget -q -O - openresty/hello-world # 这里会返回服务端地址（Pod地址），地址会变化

kubectl delete -f openresty-service.yaml

## service会自动建立endpoints， endpoints怎么可以Pod关联起来的？？
## 这里只能集群中访问，怎么集群网访问？？ hostNetwork: true ？？
```

### 部署Service （NodePort）
```
kubectl apply -f openresty-service-nodeport.yaml
# 获取service， 这里可以看到映射的端口 (PORT(S))
kubectl get service
curl your_ip:your_port/hello-world.html
curl your_ip:your_port/hello-world # 这里会返回服务端地址（Pod地址），地址会变化

kubectl delete -f openresty-service-nodeport.yaml

# 重新发布后，发现端口变化了，怎么固定端口？？
```

### 部署Service （NodePort， fixed）
```
kubectl apply -f openresty-service-nodeport-fixed.yaml
# 获取service， 这里可以看到映射的端口 (PORT(S))
kubectl get service
curl your_ip:30080/hello-world.html
curl your_ip:30080/hello-world # 这里会返回服务端地址（Pod地址），地址会变化

kubectl delete -f openresty-service-nodeport-fixed.yaml
```

### 部署Service （LoadBalancer）
```
kubectl apply -f openresty-service-loadbalancer.yaml
# 获取service， 这里可以看到映射的端口 (PORT(S)), 这个端口也是能够访问的, EXTERNAL-IP为pending 
kubectl get service

# 模拟下， 直接设置externalIPs (此处your_ip为服务器IP地址)
kubectl patch service openresty -p '{"spec":{"externalIPs":["your_ip"]}}'
# 再获取service，EXTERNAL-IP为刚才设置的ip
kubectl get service

curl your_ip/hello-world.html
curl your_ip/hello-world # 这里会返回服务端地址（Pod地址），地址会变化

kubectl delete -f openresty-service-loadbalancer.yaml
```
 
### 后置删除
```
# 在deployment目录下删除openresty
kubectl delete -f ../02-deployment/openresty-deployment-replicas10-configmap.yaml
# 在pod目录下面删除busybox（工具命令集合）
kubectl delete -f ../01-pod/busybox-pod.yaml
```
