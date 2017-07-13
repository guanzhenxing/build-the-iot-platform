# CI环境搭建——Nexus #

Nexus现在为Nexus Repository Manager OSS 3.x。

## 安装 ##

下载安装包：`wget https://sonatype-download.global.ssl.fastly.net/nexus/3/nexus-3.4.0-02-unix.tar.gz`

解压（我这边是解压到/usr/local中）:`tar -zxvf nexus-3.4.0-02-unix.tar.gz`

修改端口号：
    修改文件`/usr/local/nexus-3.4.0-02/etc/nexus-default.properties`中的application-port为你想要的端口号。

启动服务：在`/usr/local/nexus-3.4.0-02/bin`的目录下运行./nexus run &

## 注册为服务 ##

创建服务文件

    sudo vi /etc/systemd/system/nexus.service

添加如下内容

    [Unit]
    Description=nexus service
    After=network.target

    [Service]
    Type=forking
    ExecStart=/usr/local/nexus-3.4.0-02/bin/nexus start
    ExecStop=/usr/local/nexus-3.4.0-02/bin/nexus stop
    User=nexus
    Restart=on-abort

    [Install]
    WantedBy=multi-user.target

安装并启动服务

    sudo systemctl daemon-reload
    sudo systemctl enable nexus.service
    sudo systemctl start nexus.service

查看服务

    sudo systemctl status nexus.service