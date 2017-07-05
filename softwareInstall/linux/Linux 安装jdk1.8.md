# Linux 安装jdk1.8

1. 下载JDK 

```
wget http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz
```

2. 权限处理

```
chmod 777 jdk-8u111-linux-x64.tar.gz
```

3. 解压

>  tar -zxvf jdk-8u111-linux-x64.tar.gz

4.  配置环境变量
	 

```
# jdk1.8
export JAVA_HOME=/opt/jdk1.8.0_77
export JRE_HOME=/opt/jdk1.8.0_77/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$PATH
```

5.  执行 命令， 使配置生效

```
source /etc/profile
```

6. 执行命令， 查看是否配置成功

```
 java -version
```
**有问题请加QQ群： 106320003**

