## 部署Redis

### 部署redis
```
kubectl apply -f redis.yaml
# 查看生成的一些资源
kubectl get pv,pvc,pod,replicaset,statefulset,endpoints,service | grep -E "redis|NAME"

# 简单测试下reids
kubectl exec -it redis-0 -- redis-cli set test 'Hello World!'
kubectl exec -it redis-0 -- redis-cli get test
kubectl delete pod redis-0
# 重启Pod后，数据还存在
kubectl exec -it redis-0 -- redis-cli get test

kubectl delete -f redis.yaml
kubectl delete pvc data-redis-0
```
