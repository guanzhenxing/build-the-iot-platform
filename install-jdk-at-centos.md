# 在CentOS上安装JDK #

步骤1：验证是否安装了Java。`java -version`

步骤2：下载JDK。运行以下：

`wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm"`

步骤3：安装下载好的JDK。`rpm -ivh jdk-8u131-linux-x64.rpm`

步骤4：设置环境变量。运行`vim /etc/profile.d/java.sh`，然后写入以下语句：

    #!/bin/bash
    JAVA_HOME=/usr/java/jdk1.8.0_131/
    PATH=$JAVA_HOME/bin:$PATH
    export PATH JAVA_HOME
    export CLASSPATH=.

保存关闭，执行命令`chmod +x /etc/profile.d/java.sh`让它可以运行；最后执行命令`source /etc/profile.d/java.sh`让环境变量失效。

步骤5：验证。`java -version`，`echo $JAVA_HOME`
