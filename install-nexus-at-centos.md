# CI环境搭建——Nexus #

Nexus现在为Nexus Repository Manager OSS 3.x。

## 安装 ##

这里想要把Nexus安装在`/opt`目录下: `cd /opt`

下载安装包：`wget https://sonatype-download.global.ssl.fastly.net/nexus/3/nexus-3.4.0-02-unix.tar.gz`

1）解压（我这边是解压到/opt中）

    sudo tar -zxvf nexus-3.4.0-02-unix.tar.gz

2）创建链接

    sudo ln -s nexus-3.4.0-02 nexus

3）创建nexus用户

    sudo useradd nexus

4）授权

    sudo chown -R nexus:nexus /opt/nexus
    sudo chown -R nexus:nexus /opt/sonatype-work/

5）修改运行用户，这边直接用nexus用户。

    sudo vi /opt/nexus/bin/nexus.rc

    # 修改一下内容
    run_as_user="nexus"

6）修改端口号：修改文件`/opt/nexus/etc/nexus-default.properties`中的`application-port`为你想要的端口号。

7）启动服务：在`/opt/nexus/bin`的目录下运行`./nexus run &`。这一步可以忽略，也就是这一步可以不做。

8）注册为服务

    vi /etc/systemd/system/nexus.service

添加如下内容：

    [Unit]
    Description=nexus service
    After=network.target

    [Service]
    Type=forking
    ExecStart=/opt/nexus/bin/nexus start
    ExecStop=/opt/nexus/bin/nexus stop
    User=nexus
    Restart=on-abort

    [Install]
    WantedBy=multi-user.target

9）安装并启动服务

    sudo systemctl daemon-reload
    sudo systemctl enable nexus.service
    sudo systemctl start nexus.service

10）查看服务

    sudo systemctl status nexus.service

11）添加防火墙规则

---
参考：

http://qizhanming.com/blog/2017/05/16/install-sonatype-nexus-oss-33-on-centos-7

http://blog.frognew.com/2017/05/install-sonatype-nexus.html