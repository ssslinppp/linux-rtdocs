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

批量修改版本号，并提交代码
```
git clone -b develop_pek "ssh://liulin@gerrit.cmss.com:29418/BC-EPIC/epic-kingdew" && scp -p -P 29418 liulin@gerrit.cmss.com:hooks/commit-msg "epic-kingdew/.git/hooks/"
cd epic-kingdew
git branch # 查询待修改分支
git checkout release_pek_yyyyMMdd
git branch
sed -i "s/DEV-PEK-SNAPSHOT/RELEASE-PEK-20200103-SNAPSHOT/g" `grep DEV-PEK-SNAPSHOT -rl .`
git status # 查询修改后的文件，应该包括pom.xml、Dockerfile和Jenkins相关文件
git add .
git commit
git push origin release_pek_yyyyMMdd:refs/for/release_pek_yyyyMMdd
```

上述操作执行完成后，对基础model、client等进行打包打包
```
mvn clean deploy -DskipTests -f epic-base/pom.xml
mvn clean deploy -DskipTests -f epic-base/epic-base-dependeny/base-model-dependeny/pom.xml
mvn clean deploy -DskipTests -f epic-base/epic-base-dependeny/base-client-dependeny/pom.xml
mvn clean deploy -DskipTests -f epic-base/epic-base-dependeny/base-dependeny/pom.xml
```
