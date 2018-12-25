title: 关于SSH架构中Entity/Dao/Service/Controller的理解

category: java

tags:
 - java
 - ssh
 - web

date: 2017/02/24
---

# SSH框架的分层
&nbsp;&nbsp;&nbsp;&nbsp;学完了SSH框架，尽管还是不断地踩坑，但是对于整体的设计模式有了一定的了解。从作用上来说spring框架是一个Java的对象管理框架, 其主要作用是提供对象从创建到销毁的一套关系；而hibernate则是将数据库对象转化为Java对象的持久层和数据层框架，是对CRUD操作的一层封装。而spring MVC或Struts则是一个MVC的分层框架，充当路由器和分发者的角色。在SSH这样的框架基础上进行开发比起直接进行web开发面向对象的特性更明显，因此在设计模式上也具有一定的特点。
<!--more-->
&nbsp;&nbsp;&nbsp;&nbsp;在SSH框架中常见的Entity类是模型层，本质是数据表的对象化，属于数据层的封装，主要作用在于用对象来映射数据库表，主要包含对象的参数也就是数据表的表项以及所对应的set和get方法。

&nbsp;&nbsp;&nbsp;&nbsp;而DAO类则是对数据操作的一层封装，是持久层的封装，比如通过将hibernate session的方法封装为DAO类的方法来提供持久层的操作，其成员主要为session对象所提供的CRUD方法，以及封装到更高层的操作。

&nbsp;&nbsp;&nbsp;&nbsp;而Service则提供完整的业务逻辑，通过逻辑判断处理用户输入等比如判断输入是否合法，对于合法的数据和操作通过调用Dao类的方法来进行数据库访问和持久化，属于控制层或者说业务层，同时也直接连接着持久层。

&nbsp;&nbsp;&nbsp;&nbsp;而Controller则是与用户直接交互的对象，通过处理请求调用service来响应用户操作，同时给出响应和处理视图层。

&nbsp;&nbsp;&nbsp;&nbsp;在这种分层中，比较主要的一点在于将直接的数据库操作全部放在Dao层中，避免侵入Service层，而将所有的业务逻辑放在Service中，避免侵入Controller层。

其关系可以表示如下：
<image src="http://qiniu.cdn.hive-intel.com/aak1247/SSH-ENTITY_DAO_SERVICE_CONTROLLER.png"/>

# 启发

写这里时其实已经过去了很久。在编程的实践中常常容易把一些看似不太重要的细节忽略掉，比如写比较简单的业务逻辑就直接在controller中做了处理，在逐渐增加功能时就会在不经意间把controller越写越大，更重要的一点在于常常会忽略代码的重用性，这其实都是非常不好的实践。