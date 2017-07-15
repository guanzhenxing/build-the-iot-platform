# Mosquitto在CentOS上的安装与配置 #

MQTT的服务器有很多，这边暂时选择Mosquitto作为MQTT的服务器。

## 加入yum源 ##

在/etc/yum.repos.d/目录中新建一个mosquitto.repo文件，并将以下内容写入文件中：

    [home_oojah_mqtt]
    name=mqtt (CentOS_CentOS-7)
    type=rpm-md
    baseurl=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7/
    gpgcheck=1
    gpgkey=http://download.opensuse.org/repositories/home:/oojah:/mqtt/CentOS_CentOS-7//repodata/repomd.xml.key
    enabled=1

## 安装 ##

首先搜索下mosquitto的可安装包

    sudo yum search all mosquitto

    mosquitto-clients.x86_64 : Mosquitto command line publish/subscribe clients
    mosquitto-debuginfo.x86_64 : Debug information for package mosquitto
    libmosquitto-devel.x86_64 : MQTT C client library development files
    libmosquitto1.x86_64 : MQTT C client library
    libmosquittopp-devel.x86_64 : MQTT C++ client library development files
    libmosquittopp1.x86_64 : MQTT C++ client library
    mosquitto.x86_64 : MQTT version 3.1/3.1.1 compatible message broker

然后选择安装mosquitto-clients和mosquitto，也就是客户端和服务端。

    yum install mosquitto
    yum install mosquitto-clients

## 配置 ##

打开mosquitto的配置文件/etc/mosquitto/mosquitto.conf ，我们看下面的代码：

    # Place your local configuration in /etc/mosquitto/conf.d/

    pid_file /var/run/mosquitto.pid

    persistence true
    persistence_location /var/lib/mosquitto/

    #log_dest file /var/log/mosquitto/mosquitto.log

    include_dir /etc/mosquitto/conf.d
 
从上面的代码我们可以看到，自定义的配置需要配置在/etc/mosquitto/conf.d/中，文件以.conf为扩展名。

具体可以参考/etc/mosquitto/mosquitto.conf.example

## 运行服务 ##

以下是两种启动方式，二选一

    mosquitto -c /etc/mosquitto/mosquitto.conf -d
    sudo /etc/init.d/mosquitto start

## 测试 ##

1）打开一个终端窗口，运行订阅程序。

    mosquitto_sub -t mqtt

2）再打开一个终端窗口，运行发布程序。

    mosquitto_pub -h localhost -t mqtt -m "hello world."

在订阅程序的那个窗口就能看到hello world.
