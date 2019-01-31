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

---

## 基本术语
### cluster

### master
master是cluster的大脑，主要职责是`调度`，即：决定将应用放在哪里运行。

`master组件`主要包括（`这些组件只需要在master上运行即可`）：
- `kube-apiserver`：用于暴露kubernete api
- `kube-scheduler`：用于在nodes之间调度pod
- `kube-controller-manager`：管理cluster的各种资源，保证资源处于预期状态；
- `etcd`：一致性分布式键值存储，集群的数据库；
- `pod网络（如：flannel）`：保证Pod间能够相互通信。


### Node
职责：运行容器应用，由master管理;

`Node组件`主要包括（`这些组件需要在每个node上运行`）：

- `kubelet`：一个agent，当Scheduler确定在某个node上运行pod后，会将pod的具体配置信息发送到该节点的kubelet，kubelet根据这些信息创建和运行容器，并向master报告运行状态；
- `kube-proxy`: 简单说：就是实现负载均衡，Service充当pod的负载均衡，Service接收到请求后如何转发到Pod？这就是kube-proxy做的事情；
- `Pod网络（如flannel）`：保证pod之间相互通信；


### Pod
Pod是容器的集合，通常会将紧密相关的一组容器放在一个Pod中，同一个Pod中所有的容器共享IP地址和Port空间，也就是说，他们在同一个Network namespace中。

POD是kubernetes调度的最小单位，同一个Pod中的容器始终被一起调度。   
Pod中的容器会作为一个整体被master调度到一个Node上运行。    

### Controller：用于运行容器
Controller中定义了Pod的部署属性。kubernetes提供了多种Controller，包括：

- `Deployment`：最常用，可以管理Pod的多个副本；
- `ReplicaSet`：通常不会直接使用，实现了Pod的多副本管理，使用Deployment时会自动创建ReplicaSet, Deployment使用ReplicaSet来进行多副本管理；
- `DaemonSet`：用于每个Node最多只运行一个Pod副本的场景，通常用于运行daemon；
- `StatefuleSet`：能保证Pod的每个副本在整个生命周期中`名称不变`，其他的Controller不具有该特点;StatefuleSet还会保证副本按照`固定的顺序启动、更新和删除`；
- `Job`：用于`运行结束就删除`的应用，而其他Controller中的pod通常都是长期持续运行。
- 等等

### deployment
可以理解为：`应用`

### Service：用于访问容器（提供负载均衡支持）
暂时简单理解为： `端口映射`

Deployment可以部署多个Pod副本，每个Pod都有自己的IP，外界如何访问这些副本？   
Service定义了外界访问一组特定Pod的方式，Service有自己的IP和端口，Service为Pod提供了负载均衡。

Kubernetes运行容器（Pod）和访问容器（Pod）这两项任务，分别由Controller和Service执行


### Namespace
Namespace可以将一个物理Cluster逻辑划分为多个虚拟Cluster，每个cluster就是一个Namespace。不同Namespace里的资源是完全隔离的。


---

## 组件
### kubelet
运行在所有nodes上，负责启动pod和容器

### kubeadm
运行在所有nodes上,用于初始化集群

### kubectl
运行在所有nodes上，命令行工具