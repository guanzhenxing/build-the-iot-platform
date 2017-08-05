# CI环境搭建——Jenkins #

[上一篇（CI环境搭建——GitLab）](https://github.com/guanzhenxing/build-the-iot-platform/blob/master/install-gitlab-at-centos.md)我们搭建了GitLab。现在，我们来搭建Jenkins。

## Jenkins搭建 ##

### 步骤1：添加yum repos ###

运行以下命令：

    sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
    sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

### 步骤2：安装 ###

运行命令`yum install jenkins`。

### 步骤3：配置Jenkins文件 ###

主要配置Jenkins的端口等，避免与其它软件冲突。主要方法如下：

    $ sudo vim /etc/sysconfig/jenkins
    # 修改运行端口为9999，默认为8080
    JENKINS_PORT="8181"

重启Jenkins。`sudo service jenkins start`

### 步骤4：开启防火墙 ###

    firewall-cmd --zone=public --add-port=8081/tcp --permanent
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --reload

## 配置 ##