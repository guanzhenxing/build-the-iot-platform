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
- 配置rabbitmq信息，并开启cloud bus跟踪
- 配置security认证
- 将配置中心注册到服务治理中

### 客户端代码 ###

添加依赖（pom.xml）

    <dependencies>
        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>
        <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
      </dependencies>

application.yml配置文件

    spring:
      application:
        name: service-user        # 对应config server所获取的配置文件的{application}
        cloud:
          config:                   # 配置中心的相关配置
            username: dynamax       # 用户名
            password: dynamax       # 密码
            profile: dev            # profile对应config server所获取的配置文件中的{profile}
            label: master           # 指定Git仓库的分支，对应config server所获取的配置文件的{label}
            discovery:
              enabled: true         # 表示使用服务发现组件中的Config Server，而不自己指定Config Server的uri，默认false
              service-id: config-server-center # 指定Config Server在服务发现中的serviceId，默认是configserver
        bus:
          trace:
            enabled: true
    rabbitmq:                   # 配置rabbitmq
      host: 192.168.118.131
      port: 5672
      username: guest
      password: guest
    eureka:                       # 配置注册服务中心
      client:
        serviceUrl:
          defaultZone: http://localhost:1001/eureka/

以上配置中，我们完成了以下的一些事情：

- 配置了配置中心的相关信息
- 配置rabbitmq信息，并开启cloud bus跟踪
- 从服务注册中心中获得相关服务

## 配置中心的架构图 ##

<img src="resources/springcloudconfigbus.jpg"/>

## 关于高可用 ##

集群是高可用系统的一种手段。构建高可用的配置中心，包括Config Server的高可用、Git的高可用和RabbitMQ的高可用。

### Git仓库的高可用 ###

- 使用第三方库，如GitHub、git@oschina等；
- 自建GitLab仓库，并参考[GitLab仓库的高可用文档](https://about.gitlab.com/high-availability/)；

### RabbitMQ的高可用 ###

RabbitMQ的高可用可以参考[RabbitMQ的高可用文档](https://www.rabbitmq.com/ha.html)

### Config Server自身的高可用 ###

原理是使用负载均衡器，实现方式可以是把多个Config Server节点注册到Eureka Server上，然后在客户端使用Eureka Client即可。
