# 面试

## 阿里一面

1. 项目有没有做了当前rpc框架没有做到的事

2. 项目为甚么选择zookeeper

   1. zookeeper是基于CP（强一致性和分区容错性），CAP（一致性、可用性、分区容错性）
   2. BASE（基本可用、软状态、最终一致性）由CAP发展而来：核心思想在于即使无法做到强一致性，也要做到最终一致性，也是堆AP理论的补充
   3. mysql、redis和eureka都没有保证一致性

3. zookeeper是如何做到分布式一致性的

   1. 基于ZAB协议，实现了主备模式的系统架构，由单一进程处理客户端事务请求
   2. 单一进程成为leader，leader转换客户端请求为proposal，并把其发送到各个follower，当超过半数foller同意后，leader再次向follower服务器分发commit要求其提交proposal

4. 怎么保证消费者拿到的跟生产者生产的一致性

   1. watcher机制，生产者改变ip信息，及时通知订阅了的消费者信息更改

5. 学习过程中有没有遇到甚么困难

6. Object有哪些方法

7. 面向对象三大特性

8. Arraylist和linkedlist区别

9. 接口和抽象类

10. 线程的同步和调度

    1. 同步
       1. wait()、notify(),配合synchroized使用
    2. 调度
       1. Sleep()、join()、interrup()、

11. 实现线程的几种方法（拉胯）

    1. 继承Thread重写run
    2. 实现Runnable接口

12. static修辞字段和没有修辞

13. jvm的内存区域

14. jvm的堆的空间

15. mysql几种索引

16. 乐观锁和悲观锁

    1. 乐观锁大多采用数据版本机制MVCC，配合CAS解决ABA问题
    2. 悲观锁：mysql的 for update语句

17. redis为社么快

    1. 完全基于内存
    2. 数据结构简单优秀，redis自己专门设计了数据结构如跳表等
    3. 单线程
    4. 多路I/O复用

18. redis的rdb和aof

19. redis建立高性能集群

    1. ![img](Liquid面试.assets/v2-334138ba60cb9da3528f747e6b6a6d65_720w.jpg)

    2. Gossip协议的基本工作原理就是：节点彼此不断通信交换信息，一段时间后所有的节点都会知道集群完整的信息，有点类似于流言传播。

       集群中的每个节点通过一定规则挑选要通信的节点，每个节点可能知道全部节点，也可能仅知道部分节点，只要这些节点彼此可以正常通信，最终它们会达到一致的状态。

       gossip协议的好处是元数据的更新比较分散，不是集中在一个地方，这样降低了单节点的请求压力。缺点是元数据更新有延时，可能导致集群的一些操作会有一些滞后。

    3. 客服端访问规则

       1. ![img](Liquid面试.assets/20200410214742501.png)

20. 有没有阅读过框架源码（spring的模版和创建bean）

21. spring注入的几种例子

    1. autowire注入
    2. set方法注入
    3. 构造方法注入

22. 知道cdn原理吗

    1. 为了加速网站访问
    2. 需要服务商配合
    3. 主要依靠dns解析
    4. 首先用户经过本地dns系统解析转到cdn专用dns服务器
       1. cdn的dns服务器向cdn的全局负载均衡设备发送url请求
       2. 负载均衡根据用户ip返回一个所属区域的服务器ip
       3. 用户请求该ip获取资源





## 京东一面

1. 问了我3年前的实习项目，忘都忘完了
2. 项目如何实现服务注册和服务消费
3. 问难点 回答心跳机制和半包沾包
4. 问客服端如何知道服务器关闭
5. 问客户端怎么重启
   1. 用schedule延时10秒左右线程去执行客户端的连接,貌似不需要
6. java异常分类
   1. Exception和Error
      1. Exception能够捕获，Error不能捕获如OOM
   2. Exception有分为受检查异常和不受检查异常
      1. 受检查异常必须要catch或者throws才能编译通过
      2. RuntimeExcetion的子类都为不受检查如空指针、数组越界、类型转换错误、算数错误
7. 问使用过哪些中间件
8. redis对输入限制
   1. key和string的value限制为512MB
9. redis底层 答sds和skiplist
10. redis类型



## WAYZ科技一面

1. 红黑树与二叉树区别

   1. **红黑树有几种特性：**

      ​    **1.每个节点只能是红色或者黑色。**

      ​    **2.根节点必须是黑色。**

      ​    **3.红色的节点，它的叶节点只能是黑色。**

      ​    **4.从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。**

   2. 红黑树（没有规定叶子节点的高度差不大于1）：

      　　1红黑树只追求近似平衡，所以在插入与删除节点时，翻转次数远远少于平衡树，因此在需要较多插入删除操作的场景中，使用红黑树更好。

2. b树和b+树

3. 原子性、隔离性、持久性

   1. 原子性undo log
   2. 隔离性MVCC
   3. 一致性redo log

4. kafka是pull？优点缺点

   1. consumer自己决定速度
   2. 不用影响broker
   3. 缺点：如果broker没有消息，自己不断轮训阻塞

5. git工作流

   1. 一个master分支，一个develop分支
   2. master存储正式发布的历史，develop作为功能的集成分支，创建新功能应该基于develop分支
   3. 新功能完成后合并到develop分支
      1. 一旦develop做好了发布后，fork一个新分支作为发布分支用来修改bug，不做新功能开发
      2. 之后合并到master分支并分配版本号tag和更新develop分支
      3. 便于一个团队修改bug，一个团队开发新功能













# 项目

## 认证和授权

登录通过authenticationManager调用authenticate方法会去调用我们自己实现的UserDetailService的loadUserByUsername方法来判断用户是否存在

![img](Liquid面试.assets/70.png)



使用SpringSecurity，在每个controller上添加@PreAutorize注解（里面填写对应的授权验证方法），每个进来的用户都需要到该方法验证授权，根据用户传的jwt token来从redis获取用户信息，判断用户有没有对应的访问删除权限

授权根据JWt，jwt包含了头、载荷信息（uuid加密）、签名（HMAC+SHA512）

解决了单点登录问题

![在这里插入图片描述](Liquid面试.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NkbnlxZnlxZg==,size_16,color_FFFFFF,t_70.png)



### JWT问题和趋势

1、JWT默认不加密，但可以加密。生成原始令牌后，可以使用改令牌再次对其进行加密。

2、当JWT未加密方法是，一些私密数据无法通过JWT传输。

3、JWT不仅可用于认证，还可用于信息交换。善用JWT有助于减少服务器请求数据库的次数。

4、JWT的最大缺点是服务器不保存会话状态，所以在使用期间不可能取消令牌或更改令牌的权限。也就是说，一旦JWT签发，在有效期内将会一直有效。

5、JWT本身包含认证信息，因此一旦信息泄露，任何人都可以获得令牌的所有权限。为了减少盗用，JWT的有效期不宜设置太长。对于某些重要操作，用户在使用时应该每次都进行进行身份验证。

6、为了减少盗用和窃取，JWT不建议使用HTTP协议来传输代码，而是使用加密的HTTPS协议进行传输。





## xss

```
xss继承HttpServletRequestWrapper封装一个新的包装类来达到过xss过滤
```

与Filter配合，Filter把需要过滤的连接的request包装为xxsWrapper

并且需要在config的@bean注解配置filter过滤器



使用Jsoup白名单过滤，白名单选项使用basicWithImages，因为后台需要传送网站图片

```java
Whitelist.basicWithImages();
```





## 权限和菜单数据库

首页面返回数据栗子：以菜单为单位，每个菜单包含了sitelist，sitelist包含了多个站点的地址图片所属菜单等信息,展示结果根据menu_id和ordernum排序

根据导航的结果获得都是site数据，需要使用适配器配合map来映射为menu数据

并且首页输出的导航信息的菜单栏都为最子层菜单栏，也就是没有子菜单栏

```json
"menuId": 3,
            "menuName": "常用推荐",
            "menuIcon": "star",
            "parentId": 0,
            "children": [],
            "siteList": [
                {
                    "siteId": 2,
                    "siteName": "Json 格式化",
                    "menuId": 3,
                    "sitePath": "/profile/site/system/affd14d5845d9aec96a05276b8a725e7.ico",
                    "siteDescription": "在线 Json 格式化、解析、校验",
                    "siteUrl": "http://tool.geshanzsq.com/json",
                    "orderNum": null
                },
                {
                    "siteId": 23,
                    "siteName": "尔雅通识课",
                    "menuId": 3,
                    "sitePath": "/profile/site/system/c5d6b82510c991fb1fc22e24403ee4e7.png",
                    "siteDescription": "在线查询尔雅通识课答案",
                    "siteUrl": "http://tool.geshanzsq.com/erya",
                    "orderNum": null
                },
```

