## 基本命令

```
kubectl get nodes
kubectl cluster-info 

//查看服务：能看到端口映射
kubectl get services
kubectl get svc

//查看应用副本数量
kubectl get deployment

kubectl get namespace

//为node1节点添加label (key=disktype, value=ssd)
kubectl label node node1 disktype=ssd

//显示节点的Label信息
kubectl get nodes --show-labels

//删除label（key=disktype）
kubectl label node node1 disktype-

```

## replicationcontrollers
```
# 扩展rc，副本数设置为3
kubectl scale rc xxx --replicas=3
```

