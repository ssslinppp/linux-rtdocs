## Deployment

[Deployment官方](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

## Creating a Deployment
```
apiVersion: apps/v1       #当前配置格式的版本
kind: Deployment          #创建的资源类型
metadata: 
  name: nginx-deployment  #name是必须的元数据
  labels:
    app: nginx            #定义Deployment的label
spec:                     #Deployment规格说明 
  replicas: 3             #副本数量
  selector:               #定义deployment如何找到待管理的Pod
    matchLabels:
      app: nginx          #这里就是匹配下面的标记 [x] 的Pod
  template:               #定义Pod的模板，这是配置文件的重要部分
    metadata:             #Pod的原数据，至少要定义一个label  
      labels:             #label的key和value可以任意指定  
        app: nginx        #定义Pod的label  [x]
    spec:                 #Pod的规格说明，此部分定义pod中每一个【容器】的属性  
      containers:         #定义容器时，name和image是必须的  
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
      nodeSelector:       #将此Pod部署到具有 label disktype=ssd的node上
        disktype: ssd  
```


