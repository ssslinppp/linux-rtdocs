## deploy/package

构建命令
```
# 跳过测试进行deploy
mvn clean deploy -DskipTests -f pom.xml

# 跳过测试进行package，  -U：表示强制更新依赖包
mvn clean package -U -DskipTests -f pom.xml
```


