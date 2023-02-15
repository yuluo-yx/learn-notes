# mvn

## 项目常用mvn命令

```shell
mvn clean install

mvn -T 4C clean test -Dcheckstyle.skip=false -Dlicense.skip=false

运行验证
mvn validate
```

## mvn 基本命令

```shell
删除target目录
mvn clean

编译操作
主程序编译    mvn complie 		存放目录 target/classes
测试程序编译   mvn test-compile		   target/test-classes

测试操作
mvn test
测试报告存放目录 target/surefire-reports

打包操作 打jar包
mvn package

安装操作
mvn install -Dmaven.test.skip=true
将本地构建之后生成的jar包存入本地的maven仓库。路径是根据项目的maven坐标生成的

只打包不测试
mvn package -Dmaven.test.skip=true ( 跳过测试代码的编译)

mvn dependency:tree 看到依赖树

mvn dependency:analyse 查看依赖的工具

只打包不测试
mvn -Dtest package

指定端口
mvn -Dmaven.tomcat.port=9090

忽略测试失败
mvn -Dmaven.test.failure.ignore=true
```

