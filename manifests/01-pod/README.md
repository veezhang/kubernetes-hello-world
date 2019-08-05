## Pod使用
### 部署Pod
```
kubectl apply -f openresty-pod.yaml
# 查看Pod，这个可以知道Pod的IP
kubectl get pod -owide
# curl 访问下，发现在同一个局域网的其他电脑不能访问，只能本机访问
curl your_ip
# 删除Pod
kubectl delete pod openresty
kubectl delete -f openresty-pod.yaml
```

### 部署Pod（局域网中的其他机器也能访问）
```
kubectl apply -f openresty-pod-hostnetwork.yaml
# 查看Pod，这个可以知道Pod的IP，此时这个Pod是服务器的IP地址
kubectl get pod -owide
# curl 访问下，发现在同一个局域网的其他电脑也能访问
curl your_ip
# 删除Pod
kubectl delete -f openresty-pod-hostnetwork.yaml
```

### 部署Pod（Hello World!）
```
kubectl apply -f openresty-pod-hostnetwork.yaml
kubectl cp hello-world.html openresty:/usr/local/openresty/nginx/html
curl your_ip/hello-world.html

kubectl cp default.conf openresty:/etc/nginx/conf.d/default.conf
kubectl exec -it openresty -- nginx -s reload

curl your_ip/hello-world # 这里会返回服务端地址（Pod地址）

kubectl delete -f openresty-pod-hostnetwork.yaml

# 重新部署后，还能访问吗？
# 如果不能，每次部署都要cp文件进去？？ 怎么处理？？
```

### 部署Pod（Hello World! ConfigMap）
```
kubectl apply -f openresty-pod-hostnetwork-configmap.yaml

# 查看下文件，是否存在
kubectl exec -it openresty -- cat /usr/local/openresty/nginx/html/hello-world.html
kubectl exec -it openresty -- cat /etc/nginx/conf.d/default.conf

curl your_ip/hello-world.html
curl your_ip/hello-world # 这里会返回服务端地址（Pod地址）

kubectl delete -f openresty-pod-hostnetwork-configmap.yaml

# 现在这个单个Pod, 怎么扩充多个呢？
```
