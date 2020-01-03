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
