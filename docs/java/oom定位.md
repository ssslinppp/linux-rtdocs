
## 概述
某Java服务（假设PID=10765）出现了OOM，最常见的原因为：
- 有可能是内存分配确实过小，而正常业务使用了大量内存
- 某一个对象被频繁申请，却没有释放，内存不断泄漏，导致内存耗尽
- 某一个资源被频繁申请，系统资源耗尽，例如：不断创建线程，不断发起网络连接
画外音：无非“本身资源不够”“申请资源太多”“资源耗尽”几个原因


```
kubectl exec -it pod-695b96f88f-gngzr sh

/ # ps
PID   USER     TIME   COMMAND
    1 root       8:54 java -Djava.security.egd=file:/dev/./urandom -Dspring.cloud.config.uri=http://epic-config:3901 -Dspring.cloud.config.label=dev -jar /epic-blazer-DEV-
  877 root       0:00 sh
  884 root       0:00 ps
```

## 一、确认是不是内存本身就分配过小

```
jmap -heap 1
```
输出：
```
/ # jmap -heap 1
Attaching to process ID 1, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.112-b15

using thread-local object allocation.
Parallel GC with 33 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 32210157568 (30718.0MB)
   NewSize                  = 702545920 (670.0MB)
   MaxNewSize               = 10736369664 (10239.0MB)
   OldSize                  = 1405091840 (1340.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 6605504512 (6299.5MB)
   used     = 384222008 (366.4226608276367MB)
   free     = 6221282504 (5933.077339172363MB)
   5.816694353958834% used
From Space:
   capacity = 68157440 (65.0MB)
   used     = 0 (0.0MB)
   free     = 68157440 (65.0MB)
   0.0% used
To Space:
   capacity = 68157440 (65.0MB)
   used     = 0 (0.0MB)
   free     = 68157440 (65.0MB)
   0.0% used
PS Old Generation
   capacity = 4105699328 (3915.5MB)
   used     = 76364000 (72.82638549804688MB)
   free     = 4029335328 (3842.673614501953MB)
   1.8599511045344623% used

36468 interned Strings occupying 4387784 bytes.

```
如上图，可以查看新生代，老生代堆内存的分配大小以及使用情况，看是否本身分配过小


## 二、找到最耗内存的对象
命令
```
jmap -histo:live 1 | more
```
输出
```
/ # jmap -histo:live 1 |more

 num     #instances         #bytes  class name
----------------------------------------------
   1:        185342       19036168  [C
   2:         72312        6363456  java.lang.reflect.Method
   3:        183636        4407264  java.lang.String
   4:         94630        3785200  java.util.LinkedHashMap$Entry
   5:          7657        3397904  [I
   6:         95453        3054496  java.util.concurrent.ConcurrentHashMap$Node
   7:          9991        2886032  [B
   8:         41504        2707192  [Ljava.lang.Object;
   9:         32210        2449584  [Ljava.util.HashMap$Node;
  10:         17683        1951240  java.lang.Class
  11:         31631        1771336  java.util.LinkedHashMap
  12:         48706        1558592  java.util.HashMap$Node
  13:          1064        1084592  [Ljava.util.concurrent.ConcurrentHashMap$Node;
  14:         44244         932072  [Ljava.lang.Class;
  15:         37739         905736  java.util.ArrayList
  16:         23726         759232  java.lang.ref.WeakReference

```
如上图，输入命令后，会以表格的形式显示存活对象的信息，并按照所占内存大小排序：
- 实例数
- 所占内存大小
- 类名


## 三、确认是否是资源耗尽
工具：
- pstree
- netstat
查看进程创建的线程数，以及网络连接数，如果资源耗尽，也可能出现OOM


这里介绍另一种方法，通过
```
ll /proc/${PID}/fd
ll /proc/${PID}/task

ll /proc/${PID}/fd | wc -l
ll /proc/${PID}/task | wc -l （效果等同pstree -p | wc -l）
```
可以分别查看句柄详情和线程数。








