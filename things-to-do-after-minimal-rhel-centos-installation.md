# 安装最Mini的CentOS以后需要做的事情 #

网络上有一篇文章[《安装完最小化 RHEL/CentOS 7 后需要做的 30 件事情》]("https://linux.cn/article-5341-1.html")已经很详细地描述了安装最Mini版本的CentOS以后需要处理的事情。这里，只是把我在搭建IOT平台的时候需要用到的给列举出来，且此篇还将不断更新。

## 解决yum报错问题 ##

在CentOS中使用yum命令安装出现错误提示”could not retrieve mirrorlist http://mirrorlist.centos.org ***” 这是因为DNS配置错误,我装的是Cent OS 6.4 Server，没有图形界面，这个版本默认安装后，配置文件中没有配置DNS。需要通过更改配置文件来解决。方法如下：在命令提示符中输入`vi  /etc/sysconfig/network-scripts/ifcfg-eth0`用vi 打开这个文件后，接下来会出现截图的内容, 其中要注意两个配置(按下面的值去设置)。

    ONBOOT=yes
    MM_CONTROLLED=no

## 更新或升级最小化安装的 CentOS #

这样做除了更新安装已有的软件最新版本以及安全升级，不会安装任何新的软件。总的来说更新（update）和升级（upgrade）是相同的，除了事实上 升级 = 更新 + 更新时进行废弃处理。

    yum update && yum upgrade

## 配置防火墙命令 ##

在部署系统的时候，我们很多时候都需要去配置防火墙。所以，将配置防火墙的命令单独列在这里，方便以后查询。

    firewall-cmd --zone=public --add-port=端口号/tcp --permanent
    firewall-cmd --zone=public --add-service=http --permanent
    firewall-cmd --reload
    firewall-cmd --list-all

## 安装一些常用的工具 ##

以下是安装一些常用的工具，有些工具可能暂时用不到。

    yum install net-tools     //ifconfig命令的安装
    yum install wget
    yum install vim
    yum install git