# 配置中心 #

在服务化的系统中，我们往往会需要到配置中心来管理我们所需要的配置。在项目中，我们选择了Spring Cloud Config作为配置中心，这不仅仅是因为我们在项目中大量使用Spring体系，而且Spring Cloud Config的功能也是非常强大的。

## 配置中心该有哪些功能 ##

一个实用的配置中心，应该提供以下的基本功能：

- 集中管理各环境的配置文件
- 支持服务注册与发现
- 提供服务端和客户端支持
- 配置文件修改之后，可以快速的生效
- 支持各种语言
- 可以进行版本管理
- 支持大的并发查询
- 提供web管理平台进行配置和查询

## 关于Spring Cloud Config ##

Spring Cloud Config项目是一个解决分布式系统的配置管理方案。它包含了Client和Server两个部分。

- server提供配置文件的存储、以接口的形式将配置文件的内容提供出去;
- client通过接口获取数据、并依据此数据初始化自己的应用。

Spring Cloud Config可以使用Git或者SVN来管理配置文件，也可以使用本地系统管理配置文件。默认使用的是Git。

## 在项目中使用 ##

关于Spring Cloud Config的资料非常非常多，这里直接给出在项目中的主要代码。

### 服务端代码 ###

添加依赖（pom.xml）

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>

application.yml配置文件

    server:
      port: 2001
    spring:
      application:
        name: config-server-center            # 服务名
      cloud:
        config:
          server:
            git:
              uri: http://git.oschina.net/guan-zhenxing/dynamax-config
              username:
              password:
              search-paths: '{application}'   # 配置仓库路径下的相对搜索位置，这里使用的是项目名称作为占位符进行搜索。
              clone-on-start: true            # 启动的时候就clone Git仓库
              force-pull: true                # 如果本地副本是脏的，将使Spring Cloud Config Server强制从远程存储库拉
        bus:
          trace:
            enabled: true                     # 开启cloud bus的跟踪
    rabbitmq:                               # 配置rabbitmq
      host: 192.168.118.131
      port: 5672
      username: guest
      password: guest
    security:                                 # 基于SpringSecurity的认证
      basic:
        enabled: true                         # 开启基于HTTP basic的认证
      user:
       name: dynamax                         # 配置登录的账号是dynamax
       password: dynamax                     # 配置登录的密码是dynamax
    management:                               # 刷新时，关闭安全验证
      security:
        enabled: false
    eureka:                                   # 将Config Server注册到服务注册中心
      client:
        serviceUrl:
        defaultZone: http://localhost:1001/eureka/

在上面的配置中，我们主要做了以下几件事：

- 配置Git信息
- 


http://www.ityouknow.com/springcloud/2017/05/22/springcloud-config-git.html
http://www.ityouknow.com/springcloud/2017/05/23/springcloud-config-svn-refresh.html
http://www.ityouknow.com/springcloud/2017/05/25/springcloud-config-eureka.html
http://www.ityouknow.com/springcloud/2017/05/26/springcloud-config-eureka-bus.html
http://jm.taobao.org/2016/09/28/an-article-about-config-center/
http://calvin1978.blogcn.com/articles/serviceconfig.html
https://springcloud.cc/spring-cloud-dalston.html#_spring_cloud_config