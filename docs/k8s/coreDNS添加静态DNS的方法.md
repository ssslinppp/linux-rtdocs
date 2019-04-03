# coredns添加静态DNS的方法
思路：将node节点上/etc/hosts文件挂载到coredns容器中，通过修改coredns的configMap配置文件，使其能够读取到/etc/hosts文件中的内容

## 步骤1：修改coredns的configMap

执行命令：`kubectl -n kube-system edit configmap coredns`

在'health'这一行下添加如下内容：
```
        hosts /etc/add_hosts {
           fallthrough
        }
```

完整的内容如下：
```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be 
# reopened with the relevant failures.
#
apiVersion: v1
data:
  Corefile: |
    .:53 {  
        errors  
        log stdout
        health  
        hosts /etc/add_hosts {
           fallthrough
        }       
        kubernetes cluster.local 10.96.0.0/12 {
           pods insecure
        }       
        prometheus
        proxy . /etc/resolv.conf
        cache 30
    }
kind: ConfigMap
metadata:
  creationTimestamp: 2019-03-25T11:17:14Z
  name: coredns 
  namespace: kube-system
  resourceVersion: "2714953"
  selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
  uid: 8ad75e15-4eef-11e9-bd35-384c4fcc700e

```

---


## 步骤2：修改coredns的deployment

挂载node节点的/etc/hosts目录，执行命令：`kubectl -n kube-system edit deployment coredns`

在'volumeMounts:'这一行下增加如下内容：
```
        - mountPath: /etc/add_hosts
          name: add-hosts
```

在'volumes:'这一行下增加如下内容：
```
      - hostPath:
          path: /etc/hosts
          type: ""
        name: add-hosts
```

完整内容如下：
```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: 2019-03-25T11:17:14Z
  generation: 4
  labels:
    k8s-app: kube-dns
  name: coredns
  namespace: kube-system
  resourceVersion: "2716032"
  selfLink: /apis/extensions/v1beta1/namespaces/kube-system/deployments/coredns
  uid: 8aedd1f5-4eef-11e9-bd35-384c4fcc700e
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      k8s-app: kube-dns
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        k8s-app: kube-dns
    spec:
      containers:
      - args:
        - -conf
        - /etc/coredns/Corefile
        image: coredns/coredns:1.0.0
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 5
          httpGet:
            path: /health
            port: 8080
            scheme: HTTP
          initialDelaySeconds: 60
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 5
        name: coredns
        ports:
        - containerPort: 53
          name: dns
          protocol: UDP
        - containerPort: 53
          name: dns-tcp
          protocol: TCP
        - containerPort: 9153
          name: metrics
          protocol: TCP
        resources:
          limits:
            memory: 170Mi
          requests:
            cpu: 100m
            memory: 70Mi
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /etc/add_hosts
          name: add-hosts
        - mountPath: /etc/coredns
          name: config-volume
      dnsPolicy: Default
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: coredns
      serviceAccountName: coredns
      terminationGracePeriodSeconds: 30
      tolerations:
      - key: CriticalAddonsOnly
        operator: Exists
      - effect: NoSchedule
        key: node-role.kubernetes.io/master
      volumes:
      - hostPath:
          path: /etc/hosts
          type: ""
        name: add-hosts
      - configMap:
          defaultMode: 420
          items:
          - key: Corefile
            path: Corefile
          name: coredns
        name: config-volume
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: 2019-03-25T11:18:49Z
    lastUpdateTime: 2019-03-25T11:18:49Z
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: 2019-03-25T11:17:29Z
    lastUpdateTime: 2019-03-28T06:45:14Z
    message: ReplicaSet "coredns-c89d9c56b" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 4
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
```

---

## 步骤3：在所有K8S节点的/etc/hosts文件中添加自定义的静态DNS