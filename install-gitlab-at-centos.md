# CI环境搭建——GitLab #

CI（持续集成）是现在开发必不可少的一门功课。接下来，我们将要花一些时间搭建CI环境。这里，我们从GitLab开始。

## 安装 ##

参考[官方文档](https://about.gitlab.com/installation/#centos-6)进行安装。

### 步骤1：安装必要的依赖 ###

在这里先安装GitLab必要的一些依赖：

    sudo yum install curl openssh-server openssh-clients postfix cronie
    sudo service postfix start
    sudo chkconfig postfix on
    sudo lokkit -s http -s ssh

### 步骤2：下载 ###

运行以下命令可以直接更新资源以及安装gitlab。如果想安装特定的版本，可以参考官方文档。

    curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
    sudo yum install gitlab-ce

### 步骤3：配置与启动服务 ###

以下命令的作用是重新加载配置并启动服务。

    sudo gitlab-ctl reconfigure

### 步骤4：修改端口以及地址 ###

修改Nginx端口以及地址监听的操作，在文件/etc/gitlab/gitlab.rb中修改：

    nginx['listen_addresses'] = ["0.0.0.0", "[::]"] # listen on all IPv4 and IPv6 addresses
    nginx['listen_port'] = 8082

修改地址：在文件`/etc/gitlab/gitlab.rb`中修改，`external_url '你想要的地址'`

系统默认会用到8080端口作为启动时候的必须端口。如果不想用8080端口的话，可以通过这样的操作：在`/etc/gitlab/gitlab.rb`中修改`unicorn['port']=端口号`

在`/etc/gitlab/gitlab.rb`中修改`git_data_dir "/program/gitlab-data"`，更改仓库存储位置

运行`sudo gitlab-ctl reconfigure`

### 第5步：登录 ###

第一次登录GitLab的用户名密码为root和5iveL!fe。首次登录后会强制用户修改密码。

### 常用命令 ###

    sudo gitlab-ctl start    # 启动所有 gitlab 组件；
    sudo gitlab-ctl stop        # 停止所有 gitlab 组件；
    sudo gitlab-ctl restart        # 重启所有 gitlab 组件；
    sudo gitlab-ctl status        # 查看服务状态；
    sudo gitlab-ctl reconfigure        # 启动服务；
    sudo vim /etc/gitlab/gitlab.rb        # 修改默认的配置文件；
    gitlab-rake gitlab:check SANITIZE=true --trace    # 检查gitlab；
    sudo gitlab-ctl tail        # 查看日志；