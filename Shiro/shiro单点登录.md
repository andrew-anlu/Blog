## Shiro单点登录



## 单点登录

Shiro 1.2 开始提供了 Jasig CAS 单点登录的支持，单点登录主要用于多系统集成，即在多个系统中，用户只需要到一个中央服务器登录一次即可访问这些系统中的任何一个，无须多次登录。此处我们使用 Jasig CAS v4.0.0-RC3 版本：
<https://github.com/Jasig/cas/tree/v4.0.0-RC3>



Jasig CAS 单点登录系统分为服务器端和客户端，服务器端提供单点登录，多个客户端（子系统）将跳转到该服务器进行登录验证，大体流程如下：

1. 访问客户端需要登录的页面`http://localhost:9080/client/`，此时会跳转到单点登录的服务器`https://localhost:8443/server/login?service=https://localhost:9443/client/cas`；
2. 如果此时单点登录的服务器也没有登录的话，会显示登录表单页面，输入用户名 / 密码进行登录；
3. 登录成功后服务器端会回调客户端传入的地址：`https://localhost:9443/client/cas?ticket=ST-1-eh2cIo92F9syvoMs5DOg-cas01.example.org`，且带着一个 ticket；
4. 客户端会把 ticket 提交给服务器来验证 ticket 是否有效；如果有效服务器端将返回用户身份；
5. 客户端可以再根据这个用户身份获取如当前系统用户 / 角色 / 权限信息。

## 服务器端

我们使用了 Jasig CAS 服务器 v4.0.0-RC3 版本，可以到其官方的 github 下载：`https://github.com/Jasig/cas/tree/v4.0.0-RC3` 下载，然后将其 cas-server-webapp 模块封装到 shiro-example-chapter15-server 模块中，具体请参考源码。

1、数字证书使用和《第十四章 SSL》一样的数字证书，即将 localhost.keystore 拷贝到 shiro-example-chapter15-server 模块根目录下；

2、在 pom.xml 中添加 Jetty Maven 插件，并添加 SSL 支持：

```
<plugin>
  <groupId>org.mortbay.jetty</groupId>
  <artifactId>jetty-maven-plugin</artifactId>
  <version>8.1.8.v20121106</version>
  <configuration>
    <webAppConfig>
      <contextPath>/${project.build.finalName}</contextPath>
    </webAppConfig>
    <connectors>
      <connector implementation="org.eclipse.jetty.server.nio.SelectChannelConnector">
        <port>8080</port>
      </connector>
      <connector implementation="org.eclipse.jetty.server.ssl.SslSocketConnector">
        <port>8443</port>
        <keystore>${project.basedir}/localhost.keystore</keystore>
       <password>123456</password>
        <keyPassword>123456</keyPassword>
      </connector>
    </connectors>
  </configuration>
</plugin>
```



