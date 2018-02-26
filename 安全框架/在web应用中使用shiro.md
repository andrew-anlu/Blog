## 在web应用中使用shiro

[官网<https://shiro.apache.org/>](https://shiro.apache.org/)

![123](http://p1aoqp63y.bkt.clouddn.com/722072-20170429133527819-1740163446.png)





### 下载

```
<!-- shiro配置 -->
<dependency>
　　<groupId>org.apache.shiro</groupId>
   <artifactId>shiro-core</artifactId>
   <version>1.3.2</version>
</dependency>
<dependency>
   <groupId>org.apache.shiro</groupId>
   <artifactId>shiro-web</artifactId>
   <version>1.3.2</version>
</dependency>

<!-- 配置日志组件 -->
<dependency>
   <groupId>org.slf4j</groupId>
   <artifactId>slf4j-api</artifactId>
   <version>1.7.25</version>
</dependency>
<dependency>
   <groupId>org.slf4j</groupId>
   <artifactId>slf4j-jcl</artifactId>
   <version>1.7.25</version>
</dependency>
<dependency>
　　<groupId>commons-logging</groupId>
　　<artifactId>commons-logging</artifactId>
　　<version>1.2</version>
</dependency>
```





**特别地！**Shiro使用了日志框架slf4j，因此需要对应配置指定的日志实现组件，如：log4j，logback等。
而且，由于shiro-web组件使用apache commons logging组件中的工具类，所以在项目中必须添加commongs logging组件。
否则，程序启动时将会报错：



### 2.集成shiro

在Java Web应用中使用Shiro，需要特别的集成方式，不再像在非Web环境的独立应用中使用Shiro那么简单（只需要下载Shiro并添加到项目即可）。
通常，在Java Web应用中集成框架都是从配置web.xml开始的，集成Shiro也不例外。
web.xml：

```
<listener>
    <listener-class>org.apache.shiro.web.env.EnvironmentLoaderListener</listener-class>
</listener>

<filter>
    <filter-name>ShiroFilter</filter-name>
    <filter-class>org.apache.shiro.web.servlet.ShiroFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>ShiroFilter</filter-name>
    <url-pattern>/*</url-pattern>
    <dispatcher>REQUEST</dispatcher>
    <dispatcher>FORWARD</dispatcher>
    <dispatcher>INCLUDE</dispatcher>
    <dispatcher>ERROR</dispatcher>
</filter-mapping>
```



通常，在Java Web应用中集成第三方框架，都是从Filter开始。Shiro也是如此，即需要将所有请求都经过Shiro指定的Filter进行拦截，这样才能完成用户对指定资源访问的授权验证。
**\*特别地，***从Shiro 1.2+版本之后，在Java Web应用中集成Shiro非常简单，甚至都不需要明确指定shiro配置文件的路径，而是直接在web.xml中添加org.apache.shiro.web.env.EnvironmentLoaderListener即可（只需要保证在${webapp}/WEB-INF/目录下存在文件shiro.ini）。



### 3.数据源配置

在Shiro中，Realm定义了访问数据的方式，用来连接不同的数据源，如：LDAP，关系数据库，配置文件等等。
Realm类图：



![2](http://p1aoqp63y.bkt.clouddn.com/722072-20170429142537959-2011359303.png)

也就是说，可以根据实际需求及应用的权限管理复杂度灵活选择指定数据源。
在此，以org.apache.shiro.realm.text.IniRealm为例，具体配置如下：
shiro.ini：

```
[main]
# 自定义过滤器
sessionFilter = org.chench.test.shiroweb.filter.SessionFilter
authc.loginUrl = /index
ssl.enabled = false

# -----------------------------------------------------------------------------
# Users and their (optional) assigned roles
# username = password, role1, role2, ..., roleN
# -----------------------------------------------------------------------------
[users]
root = secret, admin
guest = guest, guest
presidentskroob = 12345, president
darkhelmet = ludicrousspeed, darklord, schwartz
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# roleName = perm1, perm2, ..., permN
# -----------------------------------------------------------------------------
[roles]
admin = *
schwartz = lightsaber:*
goodguy = winnebago:drive:eagle5

# -----------------------------------------------------------------------------
# The format of each line in the urls section is as follows:
# _URL_Ant_Path_Expression_ = _Path_Specific_Filter_Chain_
# -----------------------------------------------------------------------------
[urls]
/index = anon, sessionFilter
/user/signin = anon
/user/login = anon
/user/** = authc
/home/** = authc
#/admin/** = authc, roles[administrator]
#/rest/** = authc, rest
#/remoting/rpc/** = authc, perms["remote:invoke"]
```



### 4.认证

在Shiro中，认证即执行用户登录，读取指定Realm连接的数据源，以验证用户身份的有效性与合法性。
关于Shiro在Web应用中的认证流程，与Shiro在非Web环境的独立应用中的认证流程一样，都需要执行用户登录，即：

```
Subject currentUser = SecurityUtils.getSubject();
if(!currentUser.isAuthenticated()) {
　　UsernamePasswordToken token = new UsernamePasswordToken(name, password);
　　try {
　　　　currentUser.login(token);
　　} catch (UnknownAccountException e) {
       exception = e;
       logger.error(String.format("user not found: %s", name), e);
    } catch(IncorrectCredentialsException e) {
       exception = e;
       logger.error(String.format("user: %s pwd: %s error", name, password), e);
    } catch (ConcurrentAccessException e) {
       exception = e;
       logger.error(String.format("user has been authenticated: %s", name), e);
    } catch (AuthenticationException e) {
       exception = e;
       logger.error(String.format("account except: %s", name), e);
    }
}
```

唯一的区别就是，在Java Web环境中，用户名和密码参数是通过前端页面进行传递



### 5.授权

**需要再三强调！！！**Shiro作为权限框架，仅仅只能控制对资源的操作权限，并不能完成对数据权限的业务需求。
而对于Java Web环境下Shiro授权，包含个方面的含义。
其一，对于前端来说，用户只能看到他对应访问权限的元素。
其二，当用户执行指定操作（即：访问某个uri资源）时，需要验证用户是否具备对应权限。

对于第一点，在Java Web环境下，通过Shiro提供的JSP标签实现。

```
<shiro:hasRole name="admin">
    <a>用户管理</a>
</shiro:hasRole>
<shiro:hasPermission name="winnebago:drive:eagle5">
    <a>操作审计</a>
</shiro:hasPermission>
```

必须在jsp页面中引入shiro标签库：

```
<%@ taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>
```



对于第二点，与在非Java Web环境下一样，需要在后端调用API进行权限（或者角色）检验。如果在Spring框架中集成Shiro，还可以直接通过Java注解方式实现。

```
String roleAdmin = "admin";
Subject currentUser = SecurityUtils.getSubject();
if(!currentUser.hasRole(roleAdmin)) {
    //todo something
}
```