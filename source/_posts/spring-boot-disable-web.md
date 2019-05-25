title: spring boot 禁用web server

category: java

tags: 
 - spring boot

date: 2019/05/22
---

``spring boot``项目做拆分以后，一部分应用不再需要提供http服务，记录一下禁用web的方式。

<!--more-->

一行配置：``spring.main.web-environment=false``

或者在启动类中，加入：
```java
SpringApplication springApplication = 
                new SpringApplicationBuilder()
                .sources(SpringBootDisableWebEnvironmentApplication.class)
                .web(false)
                .build();

        springApplication.run(args);
```

或
```java
SpringBootDisableWebEnvironmentApplication.run(SpringBootDisableWebEnvironmentApplication.class, args); //取代 SpringApplication.run(SpringApplication.class, args)
```