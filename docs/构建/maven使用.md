## deploy/package

构建命令
```
# 跳过测试进行deploy
mvn clean deploy -DskipTests -f pom.xml

# 跳过测试进行package，  -U：表示强制更新依赖包
mvn clean package -U -DskipTests -f pom.xml
```

## 批量更改版本号
相关链接： [Use of environment variable in POM.xml doesn't work](https://stackoverflow.com/questions/26985252/use-of-environment-variable-in-pom-xml-doesnt-work)

这里没有采用上述的方式，是因为处理pom需要更新外，Dockerfile、Jenkinsfiles 也需要更新

```
git clone -b develop_dev "ssh://liulin@gerrit.cmss.com:29418/BC-EPIC/epic-kingdew" && scp -p -P 29418 liulin@gerrit.cmss.com:hooks/commit-msg "epic-kingdew/.git/hooks/"
cd epic-kingdew
sed -i "s/DEV-PEK-SNAPSHOT/RELEASE-PEK-20200103-SNAPSHOT/g" `grep DEV-PEK-SNAPSHOT -rl .`
git push xxx xxx
```

执行结果：
```
git status

#       modified:   epic-anybackup/epic-anybackup-client/pom.xml
#       modified:   epic-anybackup/epic-anybackup-model/pom.xml
#       modified:   epic-anybackup/epic-anybackup/pom.xml
#       modified:   epic-base/epic-base-dependeny/JenkinsfileAllInOneDeploy
#       modified:   epic-base/epic-base-dependeny/base-client-dependeny/pom.xml
#       modified:   epic-base/epic-base-dependeny/base-dependeny/pom.xml
#       modified:   epic-base/epic-base-dependeny/base-model-dependeny/pom.xml
#       modified:   epic-base/epic-base-dependeny/pom.xml
#       modified:   epic-base/epic-base-model/pom.xml
#       modified:   epic-base/epic-base-util/pom.xml
#       modified:   epic-base/epic-boot/pom.xml
#       modified:   epic-base/epic-config/pom.xml
#       modified:   epic-base/epic-database/pom.xml
#       modified:   epic-base/epic-frame/pom.xml
#       modified:   epic-base/epic-https/pom.xml
#       modified:   epic-base/epic-i18n/pom.xml
#       modified:   epic-base/epic-log/pom.xml
#       modified:   epic-base/epic-message-stream/pom.xml
#       modified:   epic-base/epic-mock/pom.xml
#       modified:   epic-base/epic-oauth-client/pom.xml
#       modified:   epic-base/epic-oauth-server/pom.xml
#       modified:   epic-base/epic-openstack-parent/connectors/jersey2/pom.xml
#       modified:   epic-base/epic-openstack-parent/connectors/pom.xml
#       modified:   epic-base/epic-openstack-parent/core/pom.xml
#       modified:   epic-base/epic-openstack-parent/distribution/pom.xml
#       modified:   epic-base/epic-openstack-parent/pom.xml
#       modified:   epic-base/epic-openstack4j-mock/pom.xml
#       modified:   epic-base/epic-quartz/pom.xml
#       modified:   epic-base/epic-rest-util/pom.xml
#       modified:   epic-base/epic-swagger-server/Dockerfile
#       modified:   epic-base/epic-swagger-server/pom.xml
#       modified:   epic-base/epic-workflow/epic-workflow-core/pom.xml
#       modified:   epic-base/epic-workflow/epic-workflow-extension/pom.xml
#       modified:   epic-base/epic-workflow/epic-workflow-model/pom.xml
#       modified:   epic-base/epic-workflow/pom.xml
#       modified:   epic-base/pom.xml
#       modified:   epic-blazer/Jenkinsfile
#       modified:   epic-blazer/Jenkinsfile163Deploy
#       modified:   epic-blazer/Jenkinsfile95
#       modified:   epic-blazer/Jenkinsfile95Deploy
#       modified:   epic-blazer/JenkinsfileImageSave
#       modified:   epic-blazer/epic-blazer-client/pom.xml
#       modified:   epic-blazer/epic-blazer-model/pom.xml
#       modified:   epic-blazer/epic-blazer/build21.sh
#       modified:   epic-blazer/epic-blazer/pom.xml
#       modified:   epic-blazer/epic-blazer/src/main/resources/init/Dockerfile
#       modified:   epic-blazer/epic-blazer/src/main/resources/init/deploy_dev_pek_95.sh
#       modified:   epic-blazer/pom.xml
#       modified:   epic-bull/Jenkinsfile163Deploy
#       modified:   epic-bull/JenkinsfileImageSave
#       modified:   epic-bull/epic-bull-client/pom.xml
#       modified:   epic-bull/epic-bull-core/pom.xml
#       modified:   epic-bull/epic-bull-model/pom.xml
#       modified:   epic-bull/epic-bull/pom.xml
#       modified:   epic-bull/epic-bull/src/main/resources/init/Dockerfile
#       modified:   epic-bull/pom.xml
#       modified:   epic-celtic/epic-celtic-model/pom.xml
#       modified:   epic-celtic/epic-celtic/pom.xml
#       modified:   epic-clipper/Jenkinsfile163Deploy
#       modified:   epic-clipper/JenkinsfileImageSave
#       modified:   epic-clipper/epic-clipper-client/pom.xml
#       modified:   epic-clipper/epic-clipper-model/pom.xml
#       modified:   epic-clipper/epic-clipper/pom.xml
#       modified:   epic-clipper/epic-clipper/src/main/resources/init/Dockerfile
#       modified:   epic-clipper/pom.xml
#       modified:   epic-heat/epic-heat-client/pom.xml
#       modified:   epic-heat/epic-heat-model/pom.xml
#       modified:   epic-heat/epic-heat/pom.xml
#       modified:   epic-heat/pom.xml
#       modified:   epic-hornet/epic-hornet-model/pom.xml
#       modified:   epic-hornet/epic-hornet/pom.xml
#       modified:   epic-jazz/epic-jazz-model/pom.xml
#       modified:   epic-jazz/epic-jazz/pom.xml
#       modified:   epic-king/epic-king/pom.xml
#       modified:   epic-laker/Jenkinsfile163Deploy
#       modified:   epic-laker/JenkinsfileImageSave
#       modified:   epic-laker/epic-laker-model/pom.xml
#       modified:   epic-laker/epic-laker/Dockerfile
#       modified:   epic-laker/epic-laker/pom.xml
#       modified:   epic-laker/epic-laker/src/main/resources/init/Dockerfile
#       modified:   epic-laker/laker-client/pom.xml
#       modified:   epic-laker/pom.xml
#       modified:   epic-notice/epic-notice-model/pom.xml
#       modified:   epic-notice/epic-notice/pom.xml
#       modified:   epic-notice/pom.xml
#       modified:   epic-nugget/Jenkinsfile163Deploy
#       modified:   epic-nugget/JenkinsfileImageSave
#       modified:   epic-nugget/epic-nugget-client/pom.xml
#       modified:   epic-nugget/epic-nugget-model/pom.xml
#       modified:   epic-nugget/epic-nugget/pom.xml
#       modified:   epic-nugget/epic-nugget/src/main/resources/init/Dockerfile
#       modified:   epic-nugget/pom.xml
#       modified:   epic-pacer/Jenkinsfile163Deploy
#       modified:   epic-pacer/JenkinsfileImageSave
#       modified:   epic-pacer/epic-pacer-client/pom.xml
#       modified:   epic-pacer/epic-pacer-model/pom.xml
#       modified:   epic-pacer/epic-pacer/pom.xml
#       modified:   epic-pacer/epic-pacer/src/main/resources/init/Dockerfile
#       modified:   epic-pacer/pom.xml
#       modified:   epic-piston/piston-client/pom.xml
#       modified:   epic-piston/piston-core/pom.xml
#       modified:   epic-piston/piston-model/pom.xml
#       modified:   epic-piston/piston-web/Dockerfile
#       modified:   epic-piston/piston-web/pom.xml
#       modified:   epic-piston/pom.xml
#       modified:   epic-raptor/Jenkinsfile163Deploy
#       modified:   epic-raptor/JenkinsfileImageSave
#       modified:   epic-raptor/epic-raptor-client/pom.xml
#       modified:   epic-raptor/epic-raptor-model/pom.xml
#       modified:   epic-raptor/epic-raptor/pom.xml
#       modified:   epic-raptor/epic-raptor/src/main/resources/init/Dockerfile
#       modified:   epic-raptor/pom.xml
#       modified:   epic-rocket/Jenkinsfile163Deploy
#       modified:   epic-rocket/JenkinsfileImageSave
#       modified:   epic-rocket/epic-rocket-client/pom.xml
#       modified:   epic-rocket/epic-rocket-model/pom.xml
#       modified:   epic-rocket/epic-rocket/pom.xml
#       modified:   epic-rocket/epic-rocket/src/main/resources/init/Dockerfile
#       modified:   epic-rocket/pom.xml
#       modified:   epic-spur/epic-bellamy-client/pom.xml
#       modified:   epic-spur/epic-spur-model/pom.xml
#       modified:   epic-spur/epic-spur/pom.xml
#       modified:   epic-spur/pom.xml
#       modified:   epic-sun/Jenkinsfile163Deploy
#       modified:   epic-sun/JenkinsfileImageSave
#       modified:   epic-sun/epic-sun-client/pom.xml
#       modified:   epic-sun/epic-sun-model/pom.xml
#       modified:   epic-sun/epic-sun/pom.xml
#       modified:   epic-sun/epic-sun/src/main/resources/init/Dockerfile
#       modified:   epic-sun/pom.xml
#       modified:   epic-thunder/JenkinsfileImageSave
#       modified:   epic-thunder/epic-thunder-client/pom.xml
#       modified:   epic-thunder/epic-thunder-it/pom.xml
#       modified:   epic-thunder/epic-thunder-model/pom.xml
#       modified:   epic-thunder/epic-thunder-util/pom.xml
#       modified:   epic-thunder/epic-thunder/pom.xml
#       modified:   epic-thunder/pom.xml
#       modified:   epic-warrior/Jenkinsfile163Deploy
#       modified:   epic-warrior/JenkinsfileImageSave
#       modified:   epic-warrior/epic-warrior-client/pom.xml
#       modified:   epic-warrior/epic-warrior-model/pom.xml
#       modified:   epic-warrior/epic-warrior/Dockerfile
#       modified:   epic-warrior/epic-warrior/pom.xml
#       modified:   epic-warrior/epic-warrior/src/main/resources/init/Dockerfile
#       modified:   epic-warrior/pom.xml

```

