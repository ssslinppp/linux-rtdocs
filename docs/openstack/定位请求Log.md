## 定位请求Log
描述： 使用Restful API调用后，会对应OpenStack底层一个 `req-uuid`    
如果要查看这个`req-uuid`对应的日志，可以通过如下步骤

```
# 查看虚机列表，可以找到需要的虚机uuid
nova list 

# 查询req-uuid
nova instance-action-list instanceID 

```

在OpenStack底层日志中查询该 `req-uuid`对应的日志信息   
正常日志的存放路劲： 
```
cd /var/log/nova

grep req-uuid *.log
```



