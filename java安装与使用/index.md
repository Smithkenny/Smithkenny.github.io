# Java安装与使用

# 快速开始

**系统环境:centos7**

## **安装之前先查看一下有无系统自带jdk**

```Shell
rpm -qa |grep java
rpm -qa |grep jdk
rpm -qa |grep gcj
```

### **如果有就使用批量卸载命令**

```Shell
rpm -qa | grep java | xargs rpm -e --nodeps 
```

## **使用yum安装1.8.0版本openjdk**

``` Shell
yum install java-1.8.0-openjdk* -y
```

## **查看版本**

```Shell
java -version

openjdk version "1.8.0_312"
OpenJDK Runtime Environment (build 1.8.0_312-b07)
OpenJDK 64-Bit Server VM (build 25.312-b07, mixed mode)
```

### **默认jre jdk 安装路径是/usr/lib/jvm 下面**

```Shell
ll

total 0
lrwxrwxrwx 1 root root  26 Dec 25 00:06 java -> /etc/alternatives/java_sdk
lrwxrwxrwx 1 root root  32 Dec 25 00:06 java-1.8.0 -> /etc/alternatives/java_sdk_1.8.0
lrwxrwxrwx 1 root root  40 Dec 25 00:06 java-1.8.0-openjdk -> /etc/alternatives/java_sdk_1.8.0_openjdk
drwxr-xr-x 9 root root 176 Dec 25 00:06 java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64
lrwxrwxrwx 1 root root  34 Dec 25 00:06 java-openjdk -> /etc/alternatives/java_sdk_openjdk
lrwxrwxrwx 1 root root  21 Dec 25 00:06 jre -> /etc/alternatives/jre
lrwxrwxrwx 1 root root  27 Dec 25 00:06 jre-1.8.0 -> /etc/alternatives/jre_1.8.0
lrwxrwxrwx 1 root root  35 Dec 25 00:06 jre-1.8.0-openjdk -> /etc/alternatives/jre_1.8.0_openjdk
lrwxrwxrwx 1 root root  51 Dec 25 00:06 jre-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64 -> java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/jre
lrwxrwxrwx 1 root root  29 Dec 25 00:06 jre-openjdk -> /etc/alternatives/jre_openjdk
```

## **配置全局生效**

```Shell
vim /etc/profile
#set java environment
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64

export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export PATH=$PATH:$JAVA_HOME/bin
```

## **应用配置并查看是否生效**

**source /etc/profile**

```Shell
echo $JAVA_HOME

/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64

echo $CLASSPATH

.:/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/jre/lib/rt.jar:/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/lib/dt.jar:/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/lib/tools.jar

echo $PATH

/data/kafka_2.11-2.1.0/bin:/data/kafka_2.11-2.1.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.312.b07-1.el7_9.x86_64/bin
```

## **创建一个java小程序测试下，名字叫 HelloWorld.java.输入以下命令:**

```java
public class HelloWorld {
    public static void main(String[] args){
        System.out.println("Hello World!");
    }
}
```

## **编译并执行**

```Shell
javac HelloWorld.java
java HelloWorld

Hello World!
```

## **思考：如何运行.jar这类java应用？**

### **语法如下**

```Shell
java -jar file.jar
java -jar /path/to/my/java/app.jar 
#/path/to/my/java/app.jar表示应用的路径
java -jar /path/to/my/java/app.jar arg1 arg2 
# arg1表示参数1 ，arg2表示参数2
```

### **结束了**

