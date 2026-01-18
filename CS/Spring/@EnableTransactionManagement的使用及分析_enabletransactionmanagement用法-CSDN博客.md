---
title: "@EnableTransactionManagement的使用及分析_enabletransactionmanagement用法-CSDN博客"
source: "https://blog.csdn.net/fsjwin/article/details/115575766"
author:
  - "[[成就一亿技术人!]]"
  - "[[hope_wisdom 发出的红包]]"
published:
created: 2025-07-17
description: "文章浏览阅读1.8w次，点赞9次，收藏91次。本文详细介绍了Spring框架下如何使用@EnableTransactionManagement开启事务支持，并通过@Service层方法上的@Transactional注解实现事务控制。阐述了如何选择合适的事务管理器，包括JPA和JDBC的不同场景，并展示了如何在项目中配置和使用多个事务管理器。"
tags:
  - "clippings"
---
本文详细介绍了Spring框架下如何使用@EnableTransactionManagement开启事务支持，并通过@Service层方法上的@Transactional注解实现事务控制。阐述了如何选择合适的事务管理器，包括JPA和JDBC的不同场景，并展示了如何在项目中配置和使用多个事务管理器。

摘要生成于 [C知道](https://ai.csdn.net/?utm_source=cknow_pc_ai_abstract) ，由 DeepSeek-R1 满血版支持， [前往体验 >](https://ai.csdn.net/?utm_source=cknow_pc_ai_abstract)

使用spring事务的时候有一个总开关@EnableTransactionManagement

## 1\. 事务管理器

Spring Boot 使用事务非常简单

1. 使用注解 @EnableTransactionManagement 开启事务支持后
2. 在访问数据库的Service方法上添加注解 @Transactional 便可

关于事务管理器，不管是JPA还是JDBC等都实现自接口 PlatformTransactionManager 。

1. 如果你添加的是 spring-boot-starter-jdbc 依赖，框架会默认注入 DataSourceTransactionManager 实例。
2. 如果你添加的是 spring-boot-starter-data-jpa 依赖，框架会默认注入 JpaTransactionManager 实例。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d6f02a52ec73161e2bc4522ab038b9b3.png)

## 2\. 确认加载的事务管理器是那个？

```java
@EnableTransactionManagement // 启注解事务管理，等同于xml配置方式的 <tx:annotation-driven />
@SpringBootApplication
public class ProfiledemoApplication {
 
    @Bean
    public Object testBean(PlatformTransactionManager platformTransactionManager){
        System.out.println(">>>>>>>>>>" + platformTransactionManager.getClass().getName());
        return new Object();
    }
     
    public static void main(String[] args) {
        SpringApplication.run(ProfiledemoApplication.class, args);
    }
}
java1234567891011121314
```

## 3\. 人为的指定使用哪个事务管理器

这些SpringBoot为我们自动做了，这些对我们并不透明，如果你项目做的比较大，添加的持久化依赖比较多，我们还是会选择人为的指定使用哪个事务管理器。  
代码如下：

```java
@EnableTransactionManagement
@SpringBootApplication
public class ProfiledemoApplication {
     
    // 其中 dataSource 框架会自动为我们注入
    @Bean
    public PlatformTransactionManager txManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
     
    @Bean
    public Object testBean(PlatformTransactionManager platformTransactionManager) {
        System.out.println(">>>>>>>>>>" + platformTransactionManager.getClass().getName());
        return new Object();
    }
     
    public static void main(String[] args) {
        SpringApplication.run(ProfiledemoApplication.class, args);
    }
}
java1234567891011121314151617181920
```
- 在Spring容器中，我们手工注解@Bean 将被优先加载，框架不会重新实例化其他的 PlatformTransactionManager 实现类。
- 然后在Service中，被 @ [Transactional](https://so.csdn.net/so/search?q=Transactional&spm=1001.2101.3001.7020) 注解的方法，将支持事务。如果注解在类上，则整个类的所有方法都默认支持事务。

## 4\. 多个事务管理器的选择

对于同一个工程中存在 **多个事务管理器要怎么处理** ，请看下面的实例，具体说明请看代码中的注释。

```java
@EnableTransactionManagement // 开启注解事务管理，等同于xml配置文件中的 <tx:annotation-driven />
@SpringBootApplication
public class ProfiledemoApplication implements TransactionManagementConfigurer {
     
    @Resource(name="txManager2")
    private PlatformTransactionManager txManager2;
     
    // 创建事务管理器1
    @Bean(name = "txManager1")
    public PlatformTransactionManager txManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
     
    // 创建事务管理器2
    @Bean(name = "txManager2")
    public PlatformTransactionManager txManager2(EntityManagerFactory factory) {
        return new JpaTransactionManager(factory);
    }
     
    // 实现接口 TransactionManagementConfigurer 方法，其返回值代表在拥有多个事务管理器的情况下默认使用的事务管理器
    @Override
    public PlatformTransactionManager annotationDrivenTransactionManager() {
        return txManager2;
    }
     
    public static void main(String[] args) {
        SpringApplication.run(ProfiledemoApplication.class, args);
    }
     
}
java123456789101112131415161718192021222324252627282930
```

上面定义了两个事务管理器，其中annotationDrivenTransactionManager()方法返回的是默认的事务管理器。 annotationDrivenTransactionManager()这个方法是TransactionManagementConfigurer 接口中定义的方法。源码如下：

```java
public interface TransactionManagementConfigurer {

    /**
     * Return the default transaction manager bean to use for annotation-driven database
     * transaction management, i.e. when processing {@code @Transactional} methods.
     * <p>There are two basic approaches to implementing this method:
     * <h3>1. Implement the method and annotate it with {@code @Bean}</h3>
     * In this case, the implementing {@code @Configuration} class implements this method,
     * marks it with {@code @Bean}, and configures and returns the transaction manager
     * directly within the method body:
     * <pre class="code">
     * &#064;Bean
     * &#064;Override
     * public PlatformTransactionManager annotationDrivenTransactionManager() {
     *     return new DataSourceTransactionManager(dataSource());
     * }</pre>
     * <h3>2. Implement the method without {@code @Bean} and delegate to another existing
     * {@code @Bean} method</h3>
     * <pre class="code">
     * &#064;Bean
     * public PlatformTransactionManager txManager() {
     *     return new DataSourceTransactionManager(dataSource());
     * }
     *
     * &#064;Override
     * public PlatformTransactionManager annotationDrivenTransactionManager() {
     *     return txManager(); // reference the existing {@code @Bean} method above
     * }</pre>
     * If taking approach #2, be sure that <em>only one</em> of the methods is marked
     * with {@code @Bean}!
     * <p>In either scenario #1 or #2, it is important that the
     * {@code PlatformTransactionManager} instance is managed as a Spring bean within the
     * container since most {@code PlatformTransactionManager} implementations take advantage
     * of Spring lifecycle callbacks such as {@code InitializingBean} and
     * {@code BeanFactoryAware}. Note that the same guidelines apply to
     * {@code ReactiveTransactionManager} beans.
     * @return a {@link org.springframework.transaction.PlatformTransactionManager} or
     * {@link org.springframework.transaction.ReactiveTransactionManager} implementation
     */
    TransactionManager annotationDrivenTransactionManager();

}

java12345678910111213141516171819202122232425262728293031323334353637383940414243
```

下面对两个事务管理器进行使用：

```java
@Service
public class DevSendMessageServiceImpl implements SendMessageService {
 
    // 使用value具体指定使用那个指定的事务管理器
    @Transactional(value="txManager1")
    @Override
    public void send() {
        System.out.println(">>>>>>>>Dev Send()<<<<<<<<");
        send2();
    }
     
    // 在存在多个事务管理器的情况下，如果不使用value具体指定
    // 则默认使用方法 annotationDrivenTransactionManager() 返回的事务管理器，如本例中的txManager2
    @Transactional
    public void send2() {
        System.out.println(">>>>>>>>Dev Send2()<<<<<<<<");    
    }
     
}
java12345678910111213141516171819
```

## 5\. 总结

通过上面的例子对事务管理器有了一个清晰的认识，我们知道事务管理器的话：

1. 使用注解 @EnableTransactionManagement 开启事务支持后
2. 在访问数据库的Service方法上添加注解 @Transactional 便可
3. 具体使用哪个事务管理器可以引入starter，或者自己指定。

实付 元

[使用余额支付](https://blog.csdn.net/fsjwin/article/details/)

点击重新获取

扫码支付

钱包余额 0

抵扣说明：

1.余额是钱包充值的虚拟货币，按照1:1的比例进行支付金额的抵扣。  
2.余额无法直接购买下载，可以购买VIP、付费专栏及课程。

[余额充值](https://i.csdn.net/#/wallet/balance/recharge)

举报

 [![](https://csdnimg.cn/release/blogv2/dist/pc/img/toolbar/Group.png) 点击体验  
DeepSeekR1满血版](https://ai.csdn.net/?utm_source=cknow_pc_blogdetail&spm=1001.2101.3001.10583) ![程序员都在用的中文IT技术交流社区](https://g.csdnimg.cn/side-toolbar/3.6/images/qr_app.png)

程序员都在用的中文IT技术交流社区

![专业的中文 IT 技术社区，与千万技术人共成长](https://g.csdnimg.cn/side-toolbar/3.6/images/qr_wechat.png)

专业的中文 IT 技术社区，与千万技术人共成长

![关注【CSDN】视频号，行业资讯、技术分享精彩不断，直播好礼送不停！](https://g.csdnimg.cn/side-toolbar/3.6/images/qr_video.png)

关注【CSDN】视频号，行业资讯、技术分享精彩不断，直播好礼送不停！

客服 返回顶部

![](https://i-blog.csdnimg.cn/blog_migrate/d6f02a52ec73161e2bc4522ab038b9b3.png)