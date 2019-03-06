
## backup-all.sh
备份的内容包括：  
- 备份运行服务的版本信息  
- 备份数据库  
- 备份全局配置文件  
- 备份kubernetes配置文件  

```
#!/bin/sh
#############################################
#
# 备份工具
# 
# 使用方法：将本脚本拷贝到平台服务器上，修改配置后执行
# 
#############################################

TMP_DIR=.tmp-96a2ae0c-d98f-11e7-8cce-eeb963e7b1e6

# 配置，请根据具体环境进行修改
#EPC_DATABASES="4a baby5 brook brook_cloudmaster brook_system4a_sh brook_bomc_sh chopper \
#crab ceph garp hancock nami kaido lucci lucci-province mgmt mihawk quartz tako workflow zebra shiliew eren oauth"
EPC_DATABASES="4a baby5 brook brook_cloudmaster ceph chopper crab eren garp hancock kaido lucci \
lucci-province mgmt mihawk nami oauth quartz shiliew tako workflow zebra"

MYSQL_USER=root
MYSQL_PASSWORD=Epic2wsx@WSX
MYSQL_HOST=10.10.10.19
MYSQL_PORT=30118
#########


#备份运行服务的版本信息
function backup_running_services() {
        echo "开始备份当前运行容器服务的版本号..."
        for name in `kubectl get pod | awk '{if(NR>1){ print $1 }}'`
        do
                image=`kubectl describe pod ${name} |grep Image:`
                printf "Name: %-40s \t %s \n" "$name" "$image" >> ${TMP_DIR}/services_version.txt
        done
        echo "备份完成..."

        echo "开始备份当前运行的K8s Pod..."
        kubectl get pod -o wide 1>>${TMP_DIR}/k8s_pods_info.txt
        echo "备份完成..."

        echo "开始备份当前运行的K8s Service..."
        kubectl get svc -o wide 1>>${TMP_DIR}/k8s_svc_info.txt
        echo "备份完成..."
}

#备份数据库
function backup_epc_databases() {
        echo "开始备份EPC数据库..."
        mkdir -p ${TMP_DIR}/databases
        for db in ${EPC_DATABASES}
        do
                mysqldump -u${MYSQL_USER} -p${MYSQL_PASSWORD} -h${MYSQL_HOST} -P${MYSQL_PORT} --databases \
                        $db > ${TMP_DIR}/databases/${db}.sql

                mysqldump -u${MYSQL_USER} -p${MYSQL_PASSWORD} -h${MYSQL_HOST} -P${MYSQL_PORT} --databases \
                        --no-data $db > ${TMP_DIR}/databases/${db}.schema.sql

        done
        echo "备份完成..."
}

#备份全局配置文件
function backup_epc_configs() {
        echo "开始备份EPC全局配置文件..."
		mkdir -p ${TMP_DIR}/usoppconfig
		mkdir -p ${TMP_DIR}/marmotconfig
		mkdir -p ${TMP_DIR}/nginxconfig
        cp -R /opt/data/config ${TMP_DIR}/config
		cp -R /opt/data/usopp/config/* ${TMP_DIR}/usoppconfig
		cp -R /opt/data/marmot/config/* ${TMP_DIR}/marmotconfig
		cp -R /opt/data/nginx/* ${TMP_DIR}/nginxconfig
        echo "备份完成..."
}

#备份kubernetes配置文件
function backup_kubernetes_yamls() {
        echo "开始备份kubernetes配置文件..."
        mkdir -p ${TMP_DIR}/install/kubernetes
        cp -R /opt/install/kubernetes/* ${TMP_DIR}/install/kubernetes
        echo "备份完成..."
}

# 初始化工作目录
function init_workspace() {
        rm -rf ${TMP_DIR}
        mkdir -p ${TMP_DIR}
}

# 清理工作目录
function clean_workspace() {
        rm -rf ${TMP_DIR}
}

echo "准备开始备份..."

init_workspace

backup_kubernetes_yamls

backup_epc_configs

backup_running_services

backup_epc_databases

date=`date "+%Y%m%d%H%M%S"`

CURRENT_DIR=`pwd`
cd ${TMP_DIR}/ && tar zcvf epic-backup.${date}.tar.gz ./* 
cd $CURRENT_DIR && mv ${TMP_DIR}/epic-backup.${date}.tar.gz .


clean_workspace

echo "全部备份完成..."
```