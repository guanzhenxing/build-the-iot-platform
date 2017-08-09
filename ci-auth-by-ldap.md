# CI环境搭建——GitLab、Jenkins、Nexus、JIRA等配置LDAP认证登录 #

## Jenkins配置LDAP认证登录 ##

简单介绍在Jenkins中使用LDAP进行认证的话需要哪些配置。

### 配置LDAP ###

在“系统管理 ”->“Configure Global Security”中，访问控制选择LDAP。为了预防能在万一配置不成功的情况下依然可以进行配置，可以先勾选上授权策略中的“ 任何用户可以做任何事(没有任何限制)”。

如下图配置LDAP：

<img src="/master/resources/jenkins_ldap_conf.png"/>

配置详解：

* Server：配置LDAP的服务器地址
* root DN：配置根DN
* User search base：配置用户开始查找的DN
* User search filter：用户搜索过滤器，其实就是用户的登录标识
* Group search base：配置用户的组查找的DN
* Group search filter：组搜索过滤器，其实就是组名的标识
* Manager DN：LDAP服务器的管理账号
* Manager Password：LDAP服务器的管理密码

然后点击“Test LDAP settings”，然后输入用户名密码（注意，这边的用户名为uid的值）。查看是否LDAP的配置是正确的。

然后使用LDAP中的账号登录Jenkins。

### 授权策略 ###

在LDAP中，我们新建2个Group（dev1和dev2），dev1组包含用户user1，dev2组包含用户user2。
也就是：

    dn: ou=Group,dc=dynamax,dc=io
    objectClass: organizationalUnit
    ou: Group

    dn: cn=dev1,ou=Group,dc=dynamax,dc=io
    objectClass: posixGroup
    objectClass: top
    cn: dev1
    memberUid: user1
    gidNumber: 15107

    dn: cn=dev2,ou=Group,dc=dynamax,dc=io
    objectClass: posixGroup
    objectClass: top
    cn: dev2
    memberUid: user2
    gidNumber: 32679

在Jenkins中的配置如下：

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/jenkins_ldap_auth.png"/>

<span style="color:red">注意：上图中红色框内的为选勾内容，蓝色框中的为必选内容。</span>

### 项目授权策略 ###

使用admin登录系统。新建2个job（dev1_job和dev2_job）。

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/jenkins_ldap_jobs.png"/>

如上图所示，点击“配置 ”按钮，对项目（任务）进行设置。

如下图所示，dev1_job配置用户组为dev1，dev2_job配置用户组为dev2。

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/jenkins_ldap_job_auth.png"/>

<span style="color:red">值得注意的是“ Block inheritance of global authorization matrix”一定要勾选。</span>

然后，用user1和user2登录Jenkins验证是否完成配置。

## GitLab配置LDAP认证登录 ##

关于GitLab中搭配LDAP权限的操作可以参考GitLab的文档。

下面的内容是一些简单的配置，为了就是省去看英文文档的麻烦。

在 `/etc/gitlab/gitlab.rb` 或者 `/home/git/gitlab/config/gitlab.yml` 中添加以下内容：

    gitlab_rails['ldap_enabled'] = true
    gitlab_rails['ldap_servers'] = YAML.load <<-EOS # remember to close this block with 'EOS' below
    main: # 'main' is the GitLab 'provider ID' of this LDAP server
    label: 'LDAP'
    host: '192.168.118.129'
    port: 389
    uid: 'uid'
    method: 'plain' # "tls" or "ssl" or "plain"
    allow_username_or_email_login: true
    bind_dn: 'cn=Manager,dc=dynamax,dc=io'
    password: 'gzhx0211'
    active_directory: false
    base: 'ou=People,dc=dynamax,dc=io'
    user_filter: ''
    EOS

然后运行以下命令让配置生效。
`gitlab-ctl reconfigure`

OK，这样以后你就可以使用LDAP的用户登录了。接下来问题来了，怎么分配角色呢？貌似CE版本没有提供，不过我们可以使用程序进行开发。

## Nexus配置LDAP认证登录 ##

在安装Nexus 3.3.2-02以后，默认的用户名为admin，密码为admin123。用admin登录Nexus。

在“Security”->"LDAP"中配置LDAP的信息。

### 配置连接 ###

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/nexus_ldap_connect.png"/>

点击“ Verify connection”如果连接成功则在屏幕的右上角弹出一个绿色的提示。

### 配置用户 ###

如下图显示：

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/nexus_ldap_user.png"/>

### 配置角色 ###

如下图所示：

<img src="https://raw.githubusercontent.com/guanzhenxing/build-the-iot-platform/master/resources/nexus_ldap_role.png"/>

至此，CI需要的基本上就搭建OK了。剩下的Sonar和Jira等下次再搭建。

## JIRA配置LDAP认证登录 ##
