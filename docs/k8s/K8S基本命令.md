## 基本命令

```
kubectl get nodes
kubectl cluster-info 

//查看服务：能看到端口映射
kubectl get services
kubectl get svc

//查看应用副本数量
kubectl get deployment
```


## 基本术语

### deployment
可以理解为：`应用`

### service
暂时简单理解为： `端口映射`

### pod
Pod是容器的集合，通常会将紧密相关的一组容器放在一个Pod中，同一个Pod中所有的容器共享IP地址和Port空间，也就是说，他们在同一个Network namespace中。

POD是kubernetes调度的最小单位，同一个Pod中的容器始终被一起调度。


