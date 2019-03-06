
## backup-k8s-images.sh
备份当前运行中的服务镜像

```
##########################################
# 2019年2月21日
# 备份当前运行中服务的镜像
##########################################

#!/bin/sh

time=$(date "+%Y-%m-%d-%H%M-%S")
targetDir=/home/wangguan/backup/$time/
mkdir $targetDir -p
yamlDir="/opt/install/kubernetes/"

for name in `kubectl get pod | awk '{if(NR>1){ print $1 }}'`
do
  image=`kubectl describe pod ${name} |grep Image: |awk -F ' ' '{print $2}'`
  shortName=$(echo $image| awk -F '/' '{printf "%s%s",$3, ".tar.gz\n"}')
  echo "docker save $image |gzip > $targetDir$shortName"
  docker pull $image
  docker save $image |gzip > $targetDir$shortName
  echo "--------------------------------------"
done
```