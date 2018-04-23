## SVN分支合并策略

在建立项目版本库时，可首先建好项目文件夹，并在其中建立trunk, branches, tags三个空的子目录。这样在trunk中开始进行开发

trunk是主分支，是日常开发进行的地方。

branches是分支。一些阶段性的release版本，这些版本是可以继续进行开发和维护的，则放在branches目录中.

tags是记录一些重要的里程碑版本,主要用户发布版本用.

以Eclipse为例进行测试:



### 1.在svn服务器上创建项目目录

![1](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422220312.png)

* branchs:分支文件夹,可以按照名字命名,或者功能模块起名
* tags:一些标签工程,用于发版的里程碑
* trunk:主干工程只有一个,分支会不断的往主干上合并;



### 2.创建项目

![1](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422215616.png)

创建项目名称为HelloSvn;然后上传到trank中;然后再用eclipse导入主干中的工程;



### 3.创建分支

#####右键项目 —> Team —> Branch/Tag...（分支/标记...） 

![1](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422220225.png)



　##### 设置URL两种方法:



* 手动设置分支的svn的url地址 
* 浏览资源库选择对应的目录。

手动在目录后面创建分支名称（项目名称_创建日期_版本）



### 





##### 然后 Next —> Finish

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422220358.png)

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422225246.png)

写上分支注释

##### 分支创建完成

 然后 checkout到本地分支,如下图:



![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422220534.png)

### 在分支上添加代码,并提交

![1](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422220801.png)

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422222214.png)



增加一个BrachHI的Java类,并且在hello中增加一个方法;提交代码;



### 合并分支

> 注意:一定要在主干工程上去合并分支工程;



![1](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422222533.png)



#### 选择合并策略

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422221027.png)





![55](http://p1aoqp63y.bkt.clouddn.com/55.png)







![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422222854.png)



![20](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422221422.png)





### 







![3](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422221442.png)

### 查看eclipse中的主干工程



![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422223008.png)



merge完分支的代码,要提交到svn;

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422223101.png)











### 查看服务器上的SVN

可以看到已经是最新的代码了;

![2](http://p1aoqp63y.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20180422223155.png)

## 分支的命名

分支的命令采用**branch__日期__版本号**的方式进行迭代;

例如:branch_20180423_v1.0.0.0

版本号每次增加0.0001,下次再打版本就是branch_20180523_v1.0.0.1;

发布测试版本和准生产版本以版本号为依据进行迭代测试;











## 如何打tag标签(再续)





## tag标签的命令规则





## 如果主干和分支代码冲突如何解决

