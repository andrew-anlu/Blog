

## jenkins介绍

-   它是一个自动化的周期性的集成测试过程，从检出代码、编译构建、运行测试、结果记录、测试统计等都是自动完成的，无需人工干预，有利于减少重复过程以节省时间、费用和工作量；
- 它需要有专门的集成服务器来执行集成构建；
- 它需要有代码托管工具支持，比如SVN；
- 官网地址地址：[https://jenkins.io](https://jenkins.io/)

- Jenkins的主要目标是监控软件开发流程，快速显示问题；

- jenkins持续集成中的任何一个环节都是自动完成的，无需太多的人工干预，所以它有利于减少重复过程以节省时间、费用和工作量。

本文介绍的是在CentOS系统下，用rpm包安装方式进行启动。 



## **2.1 安装准备**

| 软件    | 版本     | 说明                                    |
| ------- | -------- | --------------------------------------- |
| JDK     | 1.7.0_76 | 解压安装，注意设置好环境变量            |
| ant     | 1.9.9    | JDK1.7.x只能安装1.9.x系列ant            |
| Jenkins | 2.33     | JDK1.7.x只能安装Jenkins2.3.x系列Jenkins |

## 2.2 安装ant

ant是基于java的一款构建工具，通过配置build.xml，让项目可以进行编译，部署，打包。

因为我们要实现自动构建，所以首先要安装ant。

- 从http://ant.apache.org 上下载tar.gz版ant
- wget http://www-eu.apache.org/dist//ant/binaries/apache-ant-1.9.9-bin.tar.gz
- 解压tar包
- tar -zxvf apache-ant-1.9.9-bin.tar.gz
- 移动到/usr/share下
- cp -r apache-ant-1.9.9 /usr/share
- 切换到/usr/share目录下，重命名
- cd /usr/share
- mv apache-ant-1.9.9 ant
- 配置环境变量
- vi /etc/profile

\#set Ant enviroment

export ANT_HOME=/usr/share/ant

export PATH=PATH:PATH:ANT_HOME/bin

- 立刻将配置生效
- source /etc/proifle
- 测试ant是否生效
- ant -version

出现如下提示便表示安装成功。



![img](https://images2015.cnblogs.com/blog/847059/201707/847059-20170717182750050-778350694.png) 

ant的使用查看这篇文章：[ant在持续集成的应用](http://www.cnblogs.com/zishengY/p/7226320.html) 



**2.3 卸载及安装jenkins**

### 2.3.1 卸载原来安装的rpm包

　　首先查看是否已经安装过jenkins

```
rpm -qa|grep jenkins
```

我这里用的是jdk1.7，所以下载版本<http://pkg.jenkins-ci.org/redhat/jenkins-2.33-1.1.noarch.rpm>(jenkins下载地址：http://pkg.jenkins-ci.org/redhat/)，

   原先现在2.5版本以上，版本太高，启动报“java.lang.UnsupportedClassVersionError”错，所以要卸载之前安装的jenkins-2.54-1.1.noarch,使用如下命令



```
# 卸载原先高版本的jenkins
rpm -e nodeps jenkins-2.54-1.1.noarch
```

### 2.3.2 安装jenkins

```
# 下载jenkins-2.33-1.1.noarch.rpm
wget http://pkg.jenkins-ci.org/redhat/jenkins-2.33-1.1.noarch.rpm
#安装jenkins-2.33-1.1.noarch.rpm
sudo rpm -ih jenkins-2.33-1.1.noarch.rpm
```

出现如下图 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170714123028884-832349549.png)

表示安装成功，安装成功会自动生成下面文件：  

```
/usr/lib/jenkins/jenkins.war            #WAR包 
/etc/sysconfig/jenkins                  #配置文件
/var/lib/jenkins/                       #默认的JENKINS_HOME目录
/var/log/jenkins/jenkins.log            #Jenkins日志文件
```

## 2.4 启动

启动用如下命令：



```
sudo service jenkins start
```

 报了如下错误： 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170714123307462-966368924.png)

会报这个错误，这是由于没有配置java环境，有两种方法可以解决： 



### **2.4.1 安装jdk环境**

　　先检查一下java虚拟机有没有安装，如果没有就安装

```
java --version
```

### **2.4.2 在jenkins配置文件中配置**

　　需要“vi /etc/init.d/jenkins”，把jdk路径加上，如下：

```
# Search usable Java as /usr/bin/java might not point to minimal version required by Jenkins.
# see http://www.nabble.com/guinea-pigs-wanted-----Hudson-RPM-for-RedHat-Linux-td25673707.html
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/bin/java
/home/lutong/soft/jdk1.7.0_76/bin/java
"
for candidate in $candidates
do
  [ -x "$JENKINS_JAVA_CMD" ] && break
  JENKINS_JAVA_CMD="$candidate"
done

JAVA_CMD="$JENKINS_JAVA_CMD $JENKINS_JAVA_OPTIONS -DJENKINS_HOME=$JENKINS_HOME -jar $JENKINS_WAR"
PARAMS="--logfile=/var/log/jenkins/jenkins.log --webroot=/var/cache/jenkins/war --daemon"
```

上述配置文件中红色加粗字体的内容是我配置自己的jdk路径。由于我的系统中的java是自己解压安装的，所以我采用了第二种方式

再次启动jenkins:

![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170714124223728-1077236899.png)

# 三、默认配置修改及初始化

## 3.1 修改配置文件

上面我们有提到配置文件是/etc/sysconfig/jenkins，修改如下两项配置（根据实际需要设置）  

```
#修改为18080，默认是8080
JENKINS_PORT="18080"
#内存设置，我这里设置成如下配置
JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true -Xms512m -Xmx1024m -XX:MaxNewSize=512m -XX:MaxPermSize=1024m"
```

## 3.2 初始化

- 在浏览器中输入172.16.7.109:8080/jenkins（默认是使用8080端口）

打开jenkins的后台控制页面

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716214358128-205148242.png)

初始化成功后会自动生成一个管理员密码放到指定位置，根据页面提示复制密码粘贴到输入框就可以登录了

 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170714141717822-622133511.png)

## 3.3 初始化安装插件

- 登录成功后回让你选择插件的安装，可以选择建议的安装也可以自己进行选择，不清楚的话可以使用建议的安装

  由于建议安装的插件比较多，安装的过程有点慢，多等待一会

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170714142045509-1995538133.png)

  - 由于建议安装的插件比较多，安装的过程有点慢，多等待一会

   ![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170714142118337-693339373.png)

  安装的过程也可能因为网络等一些原因安装会失败，现在可以无视它，点击Continue，后面再进行手动的安装 

  ![2](http://p1aoqp63y.bkt.clouddn.com/2016-12-21%20at%2011.30.jpeg)

  - 安装完成
  - 安装完成后最好新创建一个管理员账户代替之前的临时自动生成的密码账户
  - 创建新的管理员账户

  ![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170716155929660-803327139%20%281%29.png)

  初始化完成，进入后台管理界面 

  

  ![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170716214545582-1050555026.png)

  初始化完成 

  ![2](http://p1aoqp63y.bkt.clouddn.com/2016-12-21%20at%2011.322.jpeg)

  后台管理界面

  ## **3.4** **初始化配置**

  ### **3.4.1 修改工作空间**

  从主页面直接到“系统管理>系统配置”，点击右边“高级”按钮

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183333035-55092743.png)

  在工作空间目录”直接修改默认工作空间目录为自定义的/home/jenkins/workspace/${ITEM_FULLNAME}，如下图： 

  ### **3.4.2 全局配置ant**

  从主页面直接到“系统管理>Global Tool Configuration”，点击右边“Ant安装”按钮，

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183351956-101901026.png)

  在name中填入名字，可以自己取，这里我填写成ant(到时Invoke Ant时，需要选择ant),ANT_HOME填入Ant的环境变量 

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183427175-1360645386.png)

   **3.4.3** **全局****配置JDK**

  从主页面直接到“系统管理>Global Tool Configuration”，点击右边“JDK安装”按钮，

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183417347-1561858092.png)

   在name中填入名字，可以自己取，这里我填写成ant(到时Invoke Ant时，需要选择ant),ANT_HOME填入Ant的环境变量 

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183451988-1617162197.png)

  ### **3.4.4 配置Credentials**

  这里主要是添加信任证书，因为我的工程的源码是放在SVN上，所以在这里我们就是要添加SVN的验证，即SVN的用户名和密码。

  从主页面左边菜单点击到“Credentials”，进入到 Credentials列表，如图所示：

  

  ![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183509956-1082587330.png)

  点击Name列中即可对Credentials中用户进行修改、新增、删除操作，如下图所示：

   

  

  ![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183525269-154596904.png)

  修改完后，点击下面“Save”保存按钮即可 

  

  # 四、建立 Jenkins 自动化持续集成项目

  ## 4.1 安装插件

  ### 4.1.1 常用插件

  一般情况下，常使用到如下这些插件：

  - **FindBugs Plug-in**： 是一个静态分析工具，它检查类或者 JAR 文件，将字节码与一组缺陷模式进行对 比以发现可能的问题。
  - **Checkstyle Plug-in**：是一个静态分析工具，检查Java程序代码。
  - **Deploy to container Plugin**：用于构建项目后，自动发布war包重新部署的插件
  - **SSH Plugin**：这个插件使用 SSH 协议执行远程 shell 命令。
  - **Multijob Plugin**：这个插件是一个将多个项目连接在一起的插件。

  ### 4.1.2 安装步骤

  　　下面以安装**Checkstyle**插件为例进行说明:

  在左上角“**系统管理**”中往下拉,找到**“管理插件”**点击进去就可以查看和管理所有的插件，点击“可选插件”显示所有jenkins支持的插件，在右上角的“过滤”输入框中，输入需要安装的插件名就可以筛选查找到想要的插件

  



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717183427175-1360645386.png)

  然后切换到“可选插件”，在右上角“过滤”框中输入checkstyle，查询结果如下

 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161820753-249838500.png)

然后，点击“直接安装”按钮，其他插件也是这样的安装方式！  



## **4.2** **建立项目**

### **4.2.1 新建项目**

下面以建立一个自由风格软件项目为例进行说明

- 点击左侧边栏的“新建”按钮，新建一个任务。
- 填写项目的名称，并选择一种构建的方式，此时我们选择第一个，构建一个自由风格的软件项目，然后点击“OK”按钮创建任务，并进行详细的配置

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716160850519-1650103209.png)

然后就会进行到下面这个配置页面

 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716160935878-992287442.png)

接下来，我从**General****、源码管理、构建触发器、构建环境、构建、构建后操作**这几个部分来进行详细配置说明 



### **4.2.2 General**

​    这部分主要是设置下名称、工作空间等。



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161034222-53368878.png)

第一步，点击高级按钮; 第二步，勾选“自定义工作空间”，输入工作空间路径; 



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161159160-1872856235.png)

若是只有一个项目，也可以直接到“系统管理>系统配置>工作空间目录”直接修改默认工作空间目录，如下图：

 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161220847-1522363124.png)



###  4.2.3 源码管理

　　因为，我们的代码是部署在SVN服务器上的，所以这里有下面三个步骤来配置jenkins监控SVN服务器代码变化。
第一步，选择Subversion；
第二步，在Repository URL输入项目SVN地址；



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161314050-1040331419.png)

第三步，在Credentials选择SVN用户名和账号，初次会需要点击Add添加，如下。

 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161341050-664320142.png)

### 4.2.4 构建触发器

指定的项目完成构建后，触发此项目的构建。

- **Poll SCM**：当选择此选项，您可以指定一个定时作业表达式来定义Jenkins每隔多久检查一下源代码	仓库的变化。如果发现变化，就执行一次构建。例如，表达式中填写H 2 * * *将使Jenkins每	隔2分                  钟就检查一次源码仓库的变化。
- **Build periodically**：此选项仅仅通知Jenkins按指定的频率对项目进行构建，而不管SCM是否有变化。 如果想在这个Job中运行一些测试用例的话，它就很有帮助。



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716161853144-1435846665.png)

### **4.2.6** **构建**

　    **这部分主要是配置构建的相关内容，用于定时触发构建或者手动执行构建的时候，对代码检验、编译时进行的操作。****构建**概念到处可查到，形象来说，构建就是要把代码从某个地方拷贝过来，编译，再拷贝到某个地方去等等操作，当然不仅与此，但是主要用来干这个。

因为我的项目是用ant脚本实现的编译和打包，所以我选择的是Invoke Ant，Ant Version选择我Ant配置的那个名字（这里可以参见3.4.2），注意不要选择default喔，那个选择了没有用。

- **增加构建步骤:Invoke Ant**
- Targets：(什么也没写，默认执行根目录下的build.xml)

如果你的构建脚本build.xml不在workspace根目录、或者说你的构建脚本不叫build.xml。那么需要在高级里设置Build File选项的路



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162022285-1737582130.png)

build.xml配置文件请查看附件“build.xml说明”，里面有每句配置说明;

checkstyleBuild.xml配置文件请查看附件“checkstyleBuild.xml说明”，里面有每句配置说明;

findBugsBuild.xml配置文件请查看附件“findBugsBuild.xml说明”，里面有每句配置说明



### 4.2.7 构建后操作

　　用于定义当前项目构建完之后的一些操作，比如构建完之后将checkstyle结果输出到指定日志文件，重新发布项目，去执行其他项目构建等。



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162207113-850922877.png)

**4.2.7.1** **构建后发布项目**

​           注意，首先你必须安装好[Deploy Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Deploy+Plugin)插件，然后在tomcat的conf目录配置tomcat-users.xml文件，如我这里配置的是manager, 在<tomcat-users>节点里添加如下内容:



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162406394-2105747705.png)

配置完之后一次war包路径、用户名、密码、主机即可

​      配置完之后一次war包路径、用户名、密码、主机即可

​      参数说明：

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162511878-1693704711.png)



- **WAR/EAR files**：war文件的存放位置，如：**/build/warDest/ad-gx-admin.war。
- l **Context path**：访问时需要输入的内容，如ad-gx-admin访问时如下：     http://172.16.4.166:10001/ ofCard/ad-gx-admin如果为空，默认是war包的名字。
- l **Container**：选择你的web容器，如tomca 7.x
- l **Manager user nam**e：填入tomcat-users.xml配置的username内容
- l **Manager password**：填入tomcat-users.xml配置的password内容
- l **Tomcat URL**：填入http://192.168.x.x:8080/ ;这个是你的服务器tomcat启动后访问的路径
- l **Deploy on failure**：构建失败依然部署，一般不选择

 　　注意：虽然这种部署方法可能会导致tomcat加载时出现卡死的现象。但是也是最简单的部署方式。如果卡死了重启下就好了，将tomcat的java内存参数调高可以解决这个问题。

最后不要忘记点击保存喔。

好了！到此一个项目的获取源码，打包，远程部署



**4.2.7.2****构建后发布静态结果**

　　输入配置文件

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162445566-315786576.png)

checkstyle-result.xml配置文件请查看附件“checkstyle-result.xml说明”，里面有配置说明。

**\*所有这些配置多做完之后，在最下方点击“保存”按钮，现在回到首页去进行构建吧！！！***





## **4.3 监控**

### **4.3.1** **主页面介绍**



![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184406503-1744205535.png)

**1、左边菜单栏**

- l **新建**：这里进入新建项目。
- l **用户**：用户管理模块，对监控系统用户的增删改查。
- l **任务历史**：以往构建过的项目。
- l **系统管理**：进去是一些配置方面的东西，进入之后几个主要的子菜单分别是[系统设置](http://172.16.4.166:18080/configure) 、[Global Tool    Configuration](http://172.16.4.166:18080/configureTools) 、[管理插件](http://172.16.4.166:18080/pluginManager) 几个模块
- l **My Views**:当前监控的项目列表。
- l **Credentials**:添加监控源码的的证书，其实就是SVN用户名和密码验证。

**2、监控项目列表**

 这里主要是Jenkins当前正在监控的项目列表。点击进去可查看当前项目详细情况



![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184427503-815120918.png)

**模块1：**

- l **状态**：最后一次构建的状态；
- l **修改记录**：代码修改记录；
- l **工作空间**：编译后代码存放的目录；
- l **立即构建**：单击此处，可立即进行构建；
- l **删除Projec**t:单击此处，可删除该项目；
- l **配置**：配置该项目相关监控信息（工作空间、chestyle规则等）；
- l **Checkstyle Warnings**：当前这次构建发现的静态警告；
- l **FindBugs Warnings**：当前这次构建发现的FindBugs。

**模块2：**

- 这里显示的最近一次构建的相关信息，是否构建成功、构建用时等**。**

**模块3：**

- l **Checkstyle Trend:**历史构建完之后的解决的代码中静态警告走势；
- l **FindBus Trend**:历史构建完之后的解决的代码中FindBugs走势

### **4.3.2** **构建状态查询**

 当任务一旦运行，您将会看到这个任务正在队列中的仪表板和当前工作主页上运行。这两种显示如下。



![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184506628-497720938.png)

一旦构建完成后，完成后的任务将会构建历史列表显示。

 

![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184524300-1439112462.png)

当然你可以在Jenkins的主控制面板上看到它，如下图。

 

![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184604081-115536023.png)

在上面展示的截图中，您将注意到有两个图标描述当前作业的状态。S栏目代表着“最新构建状态”，W栏目代表着“构建稳定性”。Jenkins使用这两个概念来介绍一个作业的总体状况：

**1****、****构建状态**:

​        下图中分级符号概述了一个Job新近一次构建会产生的四种可能的状态： 

![1](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184635628-2001157301.png)

- Successful:完成构建，且被认为是稳定的。
- Unstable:完成构建，但被认为不稳定。
- Failed:构建失败。
- Disabled:构建已禁用。

**2****、****构建稳定性**:

​        当一个Job中构建已完成并生成了一个未发布的目标构件，如果您准备评估此次构建的稳定性，Jenkins会基于一些后处理器任务为构建发布一个稳健指数 (从0-100 )，这些任务一般以插件的方式实现。它们可能包括单元测试(JUnit)、覆盖率(Cobertura )和静态代码分析(FindBugs)。分数越高，表明构建越稳定。下图中分级符号概述了稳定性的评分范围。任何构建作业的状态(总分100)低于80分就是不稳定的。

当前作业主页上还包含了一些有趣的条目。左侧栏的链接主要控制Job的配置、删除作业、构建作业。右边部分的链接指向最新的项目报告和构件。

通过点击构建历史（Build History）中某个具体的构建链接，就能跳转到Jenkins为这个构建实例而创建的构建主页上。如下图：



![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170716162207113-850922877.png)

 如果你想通过视图输出界面来监控当前任务的进展情况。你可以单击Console Output（控制台输出）。如

 果工作已完成，这将显示构建脚本产生的静态输出；如果作业仍然在运行中，Jenkins将不断刷新网页的内容，以便您可以看到它运行时的输出。如下图：



![2](http://p1aoqp63y.bkt.clouddn.com/847059-20170717184736503-1575925022.png)



# **五、常见错误处理**

## **5.1** **java.lang.UnsupportedClassVersionError**

这是因为jenkins和jdk版本不对应引起的。我这里用的是jdk1.7，所以下载版本<http://pkg.jenkins-ci.org/redhat/jenkins-2.33-1.1.noarch.rpm>(jenkins下载地址：http://pkg.jenkins-ci.org/redhat/)，   原先现在2.5版本以上，版本太高，启动报“java.lang.UnsupportedClassVersionError”错，所以要卸载之前安装的jenkins-2.54-1.1.noarch,使用如下命令



## **5.2** **command execution failed.Maybe you need to configure the job to choose one of your Ant installations?**

**“**

Started by user [admin](http://172.16.8.104:18080/user/admin)

[EnvInject] - Loading node environment variables.

Building in workspace /home/jenkins/workspace/My_cache

Updating [https://ip地址/svn/iptv/新业务/广西开机广告/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache) at revision '2017-07-17T14:14:11.377 +0800'

Using sole credentials hehaitao/****** in realm ‘<[https://ip地址:443](https://219.134.132.59/)> VisualSVN Server’

At revision 68144

No changes for [https://ip地址/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache) since the previous build

[workspace] **$ ant -file checkstyleBuild.xml -DBUILD_NUMBER=8**

**ERROR: command execution failed.Maybe you need to configure the job to choose one of your Ant installations?**

[CHECKSTYLE] Skipping publisher since build result is FAILURE

[FINDBUGS] Skipping publisher since build result is FAILURE

 

Warning: you have no plugins providing access control for builds, so falling back to legacy behavior of permitting any downstream builds to be triggered

Finished: FAILURE

**”**

　　这是由于没有成功全局配置ant的环境变量没有配置成功导致，请确保环境Ant环境变量配置成功，并且在Global Tool Configuration正确添加了Ant的路径，这个可以参见2.2及3.4.2

## **5.3** **JAVA_HOME is not defined correctly.**

**“**

**![img](https://images2015.cnblogs.com/blog/847059/201707/847059-20170717185032800-1945978892.png)控制台输出**

Started by user [admin](http://172.16.8.104:18080/user/admin)

[EnvInject] - Loading node environment variables.

Building in workspace /home/jenkins/workspace/My_cache

Updating [https://ip地址/svn/iptv/新业务/广西开机广告/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache) at revision '2017-07-17T15:33:26.714 +0800'

Using sole credentials hehaitao/****** in realm ‘<[https://ip地址:443](https://219.134.132.59/)> VisualSVN Server’

At revision 68151

[workspace] $ /home/lutong/apache-ant-1.9.9/bin/ant -file checkstyleBuild.xml -DBUILD_NUMBER=11

**Error: JAVA_HOME is not defined correctly.**

  **We cannot execute java**

Build step 'Invoke Ant' marked build as failure

[CHECKSTYLE] Skipping publisher since build result is FAILURE

[FINDBUGS] Skipping publisher since build result is FAILURE

Warning: you have no plugins providing access control for builds, so falling back to legacy behavior of permitting any downstream builds to be triggered

Finished: FAILURE

**”**

 *这是由于没有成功全局配置JDK的环境变量没有配置成功导致，请确保环境Ant环境变量配置成功，并且在Global Tool Configuration添加的JDK路径正确，这个可以参见2.4.1及3.4.3*

## **5.3** **Unable to access the repository** 

在配置“源码管理”时，如果Credentials 不选择或者选择了验证不正确，会出现这个错误，请参见3.4.4及4.2.3

## **5.4** **can****’****t create file**

**“**

Checking out [https://ip地址/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache) at revision '2017-07-17T17:46:17.618 +0800'

Using sole credentials hehaitao/****** in realm ‘<[https://ip地址:443](https://219.134.132.59/)> VisualSVN Server’

ERROR: Failed to check out [https://ip地址/svn/iptv/新业务/广西开机广告/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache)

[org.tmatesoft.svn.core.SVNException](http://stacktrace.jenkins-ci.org/search?query=org.tmatesoft.svn.core.SVNException): svn: E204899: Cannot create new file **'/home/jenkins/workspace/****广西开机广告****/.svn/lock': 权限不够**

No changes for [https://ip地址/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache](https://219.134.132.59/svn/iptv/%E6%96%B0%E4%B8%9A%E5%8A%A1/%E5%B9%BF%E8%A5%BF%E5%BC%80%E6%9C%BA%E5%B9%BF%E5%91%8A/code/ad-gx-cache) since the previous build

**”**

  这是由于 项目“广西开机广告”目录的权限不够，使用“chmod777 /home/jenkins/workspace/广西开机广告 ”即可

 

**附件：**

**\*附件一：build.xml说明***

```
<?xml version="1.0" encoding="utf-8" standalone="no"?>
<!-- 指明xml版本，编码格式为UTF-8 -->
<!-- basedir故名思意就是工作的根目录 .代表当前目录。default代表默认要做的事情。 -->
<project basedir="." default="war" name="ad-gx-admin">
    <!--
property类似定义程序中的变量，name是变量名，value是变量值，在下文中可以通过${src}和${webroot}取得变
量的值 -->
    <property name="src" value="src" />
    <property name="webroot" value="WebContent" />
    <property name="lib" value="${webroot}/WEB-INF/lib" />
    <property name="classes" value="build/classes" />
    <property name="warDest" value="build/warDest" />
    <property name="Bulid_Number" value="warDest" />
    <!-- target,即一个任务，它有一个名字，depends是它所依赖的target，
在执行这个targe，例如这里的init之前ant会先检查clean是否曾经被执行过，
如果执行过则直接直接执行init，如果没有则会先执行它依赖的target -->
    <target name="init" depends="clean">
        <!-- 创建编译后文件存放目录、war包存放目录 -->
        <mkdir dir="${classes}" />
        <mkdir dir="${warDest}" />
        <!-- 指定jar包的目录，下面直接通过${classpath}可以取得jar路径 -->
        <path id="classpath">
            <fileset dir="${lib}">
                <include name="**/*.jar" />
            </fileset>
        </path>
        <!-- 该任务主要用来对文件和目录的复制功能,即将./${src}和./${webroot}目录下
include指定的文件拷贝到./${classes}目录下。额外说明：exclude表示排除的文件 -->
        <copy todir="./${classes}">
            <fileset dir="./${src}">
                <include name="**/*.properties" />
                <include name="**/*.xml" />
                <include name="**/*.dtd" />
                <include name="**/*.json" />
                <include name="**/*.vm" />
            </fileset>
            <fileset dir="./${webroot}">
                <include name="resource/" />
            </fileset>
        </copy>
    </target>
    <target name="clean">
        <!-- 该任务的作用是根据日志或监控器的级别输出信息。它包括message、file、append和level四个属性 -
->
<echo >${warDest}</echo>
<!== 对文件或目录进行删除,这里是删除${warDest}和${classes}目录 -->
        <delete dir="${warDest}" />
        <delete dir="${classes}" />
    </target>
    <target name="complie" depends="init">
        <!-- 编译:将srcdir目录的源码，编译后放到destdir目录下,refid表示关联的jar包 -->
        <javac srcdir="${src}" destdir="${classes}" debug="true" debuglevel="lines,vars,source" encoding="UTF-8">
            <classpath refid="classpath" />
        </javac>
    </target>
    <target name="war" depends="complie">
        <war warfile="${warDest}/ad-gx-admin.war" webxml="${webroot}/WEB-INF/web.xml">
            <lib dir="${lib}" />
            <classes dir="${classes}" />
            <fileset dir="./${webroot}">
                <include name="**/**" />
                <exclude name="META-INF/**" />
                <exclude name="WEB-INF/classes/**" />
                <exclude name="WEB-INF/lib/**" />
            </fileset>
        </war>
    </target>
</project>
```

 **\*附件二：checkstyle-result.xml说明***

 ```
<?xml version="1.0" encoding="utf-8"?>
<project name="checkstyle" default="checkstyle" basedir=".">
    <!-- 检查源码的路径 -->
    <target name="init">
        <tstamp />
        <!-- 输出报告的路径  -->
        <property name="project.dir" value="/home/jenkins/workspace/广西开机广告项目/checkstyle_report" />
        <property name="project.checkstyle.report.dir" value="${project.dir}/${BUILD_NUMBER}" />
        <property name="project.checkstyle.result.name" value="checkstyle-result.xml" />
        <property name="project.checkstyle.report.name" value="checkstyle-report.html" />
        <!-- 检测规则存放路径  -->
        <property name="checkstyle.config" value="/home/jenkins/workspace/checkstyle.xml" />
        <property name="checkstyle.report.style" value="/var/lib/jenkins/plugins/checkstyle/WEB-INF/lib/checkstyle-frames.xsl" />
        <property name="checkstyle.result" value="${project.checkstyle.report.dir}/${project.checkstyle.result.name}" />
        <property name="checkstyle.report" value="${project.checkstyle.report.dir}/${project.checkstyle.report.name}" />
        <mkdir dir="${project.checkstyle.report.dir}" />
    </target>
    <taskdef resource="checkstyletask.properties" classpath="/var/lib/jenkins/plugins/checkstyle/WEB-INF/lib/checkstyle-6.5-all.jar" />
    <target name="checkstyle" depends="init" description="check java code and report.">
        <checkstyle config="${checkstyle.config}" failureProperty="checkstyle.failure" failOnViolation="false">
            <formatter type="xml" tofile="${checkstyle.result}" />
            <fileset dir="/home/jenkins/workspace/广西开机广告项目" includes="**/*.java" />
            <!-- 检查源代码的存放路径 -->
        </checkstyle>
        <!--<style in="${checkstyle.result}" out="${checkstyle.report}" style="${checkstyle.report.style}" />
         通过指定的xsl模版文件生成一份html的报告，这里生成的文件用于邮件发送时附加上，另外Jenkins插件也会生成可视化的结果  -->
    </target>
    <target name="xml2html" depends="checkstyle">
        <!-- 将生产结果根据扩展样式表文件checkstyle-frames.xsl生成html页面,输出到html -->
        <xslt in="${checkstyle.result}" out="${checkstyle.report}" style="${checkstyle.report.style}"></xslt>
    </target>
</project>
 ```

