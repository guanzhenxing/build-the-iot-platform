# CI环境搭建——LDAP #

轻型目录存取协定（Lightweight Directory Access Protocol,LDAP）是一个开放的，中立的，工业标准的应用协议，通过IP协议提供访问控制和维护分布式信息的目录信息。GitLab、Jenkins、Nexus等都支持LDAP认证。

## 步骤1：安装 ##

检查是否安装：`rpm -qa | grep openldap`

安装：

    yum install -y openldap openldap-clients openldap-servers migrationtools
    cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG
    chown ldap. /var/lib/ldap/DB_CONFIG
    systemctl start slapd
    systemctl enable slapd

验证端口：`netstat -tlnp | grep slapd`

## 步骤2：设置 OpenLDAP 的管理员密码 ##

生成处理以后的管理员密码：

    [root@bogon ~]# slappasswd
    New password:
    Re-enter new password:
    {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

新建文件`chrootpw.ldif`。{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx为你在刚才生成的密码。

    dn: olcDatabase={0}config,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

执行`ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif`

## 步骤3：导入基本的Schema ##

    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
    ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif

## 步骤4：设置自己的Domain Name ##

首先要生成经处理后的目录管理者明文密码。

    slappasswd
    New password:
    Re-enter new password:
    {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

新建文件。chdomain.ldif，并把以下内容写入文件。（需要把"dc=***,dc=***"替换成你自己的dc内容，需要把{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx替换成刚才生成的密码）

    dn: olcDatabase={1}monitor,cn=config
    changetype: modify
    replace: olcAccess
    olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
    read by dn.base="cn=Manager,dc=dynamax,dc=io" read by * none

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcSuffix
    olcSuffix: dc=dynamax,dc=io

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    replace: olcRootDN
    olcRootDN: cn=Manager,dc=dynamax,dc=io

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcRootPW
    olcRootPW: {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

    dn: olcDatabase={2}hdb,cn=config
    changetype: modify
    add: olcAccess
    olcAccess: {0}to attrs=userPassword,shadowLastChange by
    dn="cn=Manager,dc=dynamax,dc=io" write by anonymous auth by self write by * none
    olcAccess: {1}to dn.base="" by * read
    olcAccess: {2}to * by dn="cn=Manager,dc=dynamax,dc=io" write by * read

执行：`ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif`

再新建文件：`basedomain.ldif`，并将以下内容写入。并将一些dc替换成你自己的。

    dn: dc=dynamax,dc=io
    objectClass: top
    objectClass: dcObject
    objectclass: organization
    o: Dynamax
    dc: Dynamax

    dn: cn=Manager,dc=dynamax,dc=io
    objectClass: organizationalRole
    cn: Manager
    description: Directory Manager

    dn: ou=people,dc=dynamax,dc=io
    objectClass: organizationalUnit
    ou: people

    dn: ou=groups,dc=dynamax,dc=io
    objectClass: organizationalUnit
    ou: groups

执行语句：`ldapadd -x -D cn=Manager,dc=dynamax,dc=io -W -f basedomain.ldif` （注意dc之类的替换）

## 步骤5：允许防火墙访问 LDAP 服务 ##

        firewall-cmd --add-service=ldap --permanent
        firewall-cmd --reload

## 步骤6：查询 ##

    ldapsearch -x -b "dc=dynamax,dc=io" -H ldap://192.168.118.130

## 步骤7：安装客户端 ##

选择有：phpLDAPadmin等。