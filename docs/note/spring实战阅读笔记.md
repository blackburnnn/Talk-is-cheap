jpa
## 11.2　Spring与Java持久化API

Java持久化API（Java Persistence API，JPA）诞生在EJB 2实体Bean的废墟之上，并成为下一代Java持久化标准。JPA是基于POJO的持久化机制，它从Hibernate和Java数据对象（Java Data Object，JDO）上借鉴了很多理念并加入了Java 5注解的特性。

在Spring 2.0版本中，Spring首次集成了JPA的功能。具有讽刺意味的是，很多人批评（或赞赏）Spring颠覆了EJB。但是，当Spring支持JPA后，很多开发人员都推荐在基于Spring的应用程序中使用JPA实现持久化。实际上，有些人还将Spring-JPA的组合称为POJO开发的梦之队。

在Spring中使用JPA的第一步是要在Spring应用上下文中将实体管理器工厂（entity manager factory）按照bean的形式来进行配置。

### 11.2.1　配置实体管理器工厂

简单来讲，基于JPA的应用程序需要使用EntityManagerFactory的实现类来获取EntityManager实例。JPA定义了两种类型的实体管理器：
 
- 应用程序管理类型（Application-managed）：当应用程序向实体管理器工厂直接请求实体管理器时，工厂会创建一个实体管理器。在这种模式下，程序要负责打开或关闭实体管理器并在事务中对其进行控制。这种方式的实体管理器适合于不运行在Java EE容器中的独立应用程序。
- 容器管理类型（Container-managed）：实体管理器由Java EE创建和管理。应用程序根本不与实体管理器工厂打交道。相反，实体管理器直接通过注入或JNDI来获取。容器负责配置实体管理器工厂。这种类型的实体管理器最适用于Java EE容器，在这种情况下会希望在persistence.xml指定的JPA配置之外保持一些自己对JPA的控制。

以上的两种实体管理器实现了同一个EntityManager接口。关键的区别不在于EntityManager本身，而是在于EntityManager的创建和管理方式。应用程序管理类型的EntityManager是由EntityManagerFactory创建的，而后者是通过PersistenceProvider的createEntityManagerFactory()方法得到的。与此相对，容器管理类型的Entity ManagerFactory是通过PersistenceProvider的createContainerEntityManager Factory()方法获得的。

这对想使用JPA的Spring开发者来说又意味着什么呢？其实这并没太大的关系。不管你希望使用哪种EntityManagerFactory，Spring都会负责管理EntityManager。如果你使用的是应用程序管理类型的实体管理器，Spring承担了应用程序的角色并以透明的方式处理EntityManager。在容器管理的场景下，Spring会担当容器的角色。

这两种实体管理器工厂分别由对应的Spring工厂Bean创建：

 
- LocalEntityManagerFactoryBean生成应用程序管理类型的EntityManager-Factory；
- LocalContainerEntityManagerFactoryBean生成容器管理类型的Entity-ManagerFactory。

需要说明的是，选择应用程序管理类型的还是容器管理类型的EntityManager Factory，对于基于Spring的应用程序来讲是完全透明的。当组合使用Spring和JPA时，处理EntityManagerFactory的复杂细节被隐藏了起来，数据访问代码只需关注它们的真正目标即可，也就是数据访问。

应用程序管理类型和容器管理类型的实体管理器工厂之间唯一值得关注的区别是在Spring应用上下文中如何进行配置。让我们先看看如何在Spring中配置应用程序管理类型的LocalEntityManagerFactoryBean，然后再看看如何配置容器管理类型的LocalContainerEntityManagerFactoryBean。

#### 配置应用程序管理类型的JPA

对于应用程序管理类型的实体管理器工厂来说，它绝大部分配置信息来源于一个名为persistence.xml的配置文件。这个文件必须位于类路径下的META-INF目录下。

persistence.xml的作用在于定义一个或多个持久化单元。持久化单元是同一个数据源下的一个或多个持久化类。简单来讲，persistence.xml列出了一个或多个的持久化类以及一些其他的配置如数据源和基于XML的配置文件。如下是一个典型的persistence.xml文件，它是用于Spittr应用程序的：

```xml
<persistence xmlns="https://java.sun.com/xml/ns/persistence"
    version="1.0">
    <persistence-unit name="spitterPU">
        <class>com.habuma.spittr.domain.Spitter</class>
        <class>com.habuma.spittr.domain.Spittle</class>
        <properties>
            <property name="toplink.jdbc.driver"
                value="org.hsqldb.jdbcDriver" />
            <property name="toplink.jdbc.url"
                value="jdbc:hsqldb:hsql://localhost/spitter/spitter" />
            <property name="toplink.jdbc.user"
                value="sa" />
            <property name="toplink.jdbc.password"
                value="" />
        </properties>
    </persistence-unit>
</persistence>
```


因为在persistence.xml文件中包含了大量的配置信息，所以在Spring中需要配置的就很少了。可以通过以下的@Bean注解方法在Spring中声明LocalEntityManagerFactoryBean：

```java
@Bean
public LocalEntityManagerFactoryBean entityManagerFactoryBean(){
    LocalEntityManagerFactoryBean emfb
        = new LocalEntityManagerFactoryBean();
    emfb.setPersistenceUnitName("apitterPU");
    return emfb;
}
```

赋给persistenceUnitName属性的值就是persistence.xml中持久化单元的名称。

创建应用程序管理类型的EntityManagerFactory都是在persistence.xml中进行的，而这正是应用程序管理的本意。在应用程序管理的场景下（不考虑Spring时），完全由应用程序本身来负责获取EntityManagerFactory，这是通过JPA实现的PersistenceProvider做到的。如果每次请求EntityManagerFactory时都需要定义持久化单元，那代码将会迅速膨胀。通过将其配置在persistence.xml中，JPA就能够在这个特定的位置查找持久化单元定义了。

但借助于Spring对JPA的支持，我们不再需要直接处理PersistenceProvider了。因此，再将配置信息放在persistence.xml中就显得不那么明智了。实际上，这样做妨碍了我们在Spring中配置EntityManagerFactory（如果不是这样的话，我们可以提供一个Spring配置的数据源）。

鉴于以上的原因，让我们关注一下容器管理的JPA：

#### 使用容器管理类型的JPA

容器管理的JPA采取了一个不同的方式。当运行在容器中时，可以使用容器（在我们的场景下是Spring）提供的信息来生成EntityManagerFactory。

你可以将数据源信息配置在Spring应用上下文中，而不是在persistence.xml中了。例如，如下的@Bean注解方法声明了在Spring中如何使用LocalContainerEntity-ManagerFactoryBean来配置容器管理类型的JPA：

```java
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory(
        DataSource dataSource,JpaVendorAdapter jpaVendorAdatper){
    LocalContainerEntityManagerFactoryBean emfb =
        new LocalContainerEntityManagerFactoryBean();
    emfb.setDataSrouce(dataSrouce);
    emfb.setJpaVendorAdapter(jpaVendorAdapter);
    return emfb;
}
```

这里，我们使用了Spring配置的数据源来设置dataSource属性。任何javax.sql.DataSource的实现都是可以的。尽管数据源还可以在persistence.xml中进行配置，但是这个属性指定的数据源具有更高的优先级。

jpaVendorAdapter属性用于指明所使用的是哪一个厂商的JPA实现。Spring提供了多个JPA厂商适配器：

 
- EclipseLinkJpaVendorAdapter
- HibernateJpaVendorAdapter
- OpenJpaVendorAdapter
- TopLinkJpaVendorAdapter（在Spring 3.1版本中，已经将其废弃了）

在本例中，我们使用Hibernate作为JPA实现，所以将其配置为Hibernate-JpaVendorAdapter：

```java
public JpaVendorAdapter jpaVendorAdapter(){
    HibernateJpaVendorAdapter adapter = new HibernateJpaVendorAdapter();
    adapter.setDatabase("HSQL");
    adapter.setShowSql(true);
    adapter.setGenerateDdl(false);
    adapter.setDatabasePlatform("org.hibernate.dialect.HSQLDialect");
    return adapter;
}
```

有多个属性需要设置到厂商适配器上，但是最重要的是database属性，在上面我们设置了要使用的数据库是Hypersonic。这个属性支持的其他值如表11.1所示。
表11.1　Hibernate的JPA适配器支持多种数据库，可以通过其database属性配置使用哪个数据库

数据库平台|属性database的值
---|---
IDM DB2 | DB2
Apache Derby|DERBY
H2|H2
Hypersonic|HSQL
Informix|INFORMIX
MySQL|MYSQL
Oracle|ORACLE
PostgreSQL|POSTGRESQL
Microsoft SQL Server |SQL SERVER
Sybase|SYBASE

一些特定的动态持久化功能需要对持久化类按照指令（instrumentation）进行修改才能支持。在属性延迟加载（只在它们被实际访问时才从数据库中获取）的对象中，必须要包含知道如何查询未加载数据的代码。一些框架使用动态代理实现延迟加载，而有一些框架像JDO，则是在编译时执行类指令。

选择哪一种实体管理器工厂主要取决于如何使用它。但是，下面的小技巧可能会让你更加倾向于使用LocalContainerEntityManagerFactoryBean。

persistence.xml文件的主要作用就在于识别持久化单元中的实体类。但是从Spring 3.1开始，我们能够在LocalContainerEntityManagerFactoryBean中直接设置packagesToScan属性：

```java
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory(
        DataSource dataSource,JpaVendorAdapter jpaVendorAdapter){
    LocalContainerEntityManagerFactory Bean emfb = 
        new LocalContainerEntityManagerFactoryBean();
    emfb.setDataSource(dataSource);
    emfb.setJpaVendorAdapter(jpaVendorAdatper);
    emfb.setPackagesToScan("com.habuma.spittr.domain");
    return emfb;
}
```

在这个配置中，LocalContainerEntityManagerFactoryBean会扫描com.habuma.spittr.domain包，查找带有@Entity注解的类。因此，没有必要在persistence.xml文件中进行声明了。同时，因为DataSource也是注入到LocalContainer-EntityManager FactoryBean中的，所以也没有必要在persistence.xml文件中配置数据库信息了。那么结论就是，persistence.xml文件完全没有必要存在了！你尽可以将其删除，让LocalContainerEntityManagerFactoryBean来处理这些事情。

#### 从JNDI获取实体管理器工厂

还有一件需要注意的事项，如果将Spring应用程序部署在应用服务器中，EntityManagerFactory可能已经创建好了并且位于JNDI中等待查询使用。在这种情况下，可以使用Spring jee命名空间下的<jee:jndi-lookup>元素来获取对EntityManagerFactory的引用：

```xml
<jee:jndi-lookup id="emf" jndi-name="persistence/spitterPU" />
```

我们也可以使用如下的Java配置来获取EntityManagerFactory：

```java
@Bean
public JndiObjectFactoryBean entityManagerFactory(){
    JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
    jndiObjectFB.setJndiName("jdbc/SpittrDS");
    return jndiObjectFB;
}
```

尽管这种方法没有返回EntityManagerFactory，但是它的结果就是一个EntityManagerFactory bean。这是因为它所返回的JndiObjectFactoryBean是FactoryBean接口的实现，它能够创建EntityManagerFactory。

不管你采用何种方式得到EntityManagerFactory，一旦得到这样的对象，接下来就可以编写Repository了。让我们开始吧。

### 11.2.2　编写基于JPA的Repository

正如Spring对其他持久化方案的集成一样，Spring对JPA集成也提供了JpaTemplate模板以及对应的支持类JpaDaoSupport。但是，为了实现更纯粹的JPA方式，基于模板的JPA已经被弃用了。这与我们在11.1.2小节使用的Hibernate上下文Session是很类似的。

鉴于纯粹的JPA方式远胜于基于模板的JPA，所以在本节中我们将会重点关注如何构建不依赖Spring的JPA Repository。如下程序清单中的JpaSpitterRepository展现了如何开发不使用Spring JpaTemplate的JPA Repository。

## 程序清单11.2　不使用Spring模板的纯JPA Repository

```java
package com.habuma.spittr.persistence;
import java.util.List;
import javax.persistence.EntityManagerFactory;
import javax.persistence.PersistenceUnit;
import org.springframework.dao.DataAccessException;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import com.habuma.spittr.domain.Spitter;
import com.habuma.spittr.domain.Spittle;

@Repository
@Transactional
public class JpaSpitterRepository implements SpitterRepository {
    @PersistenceUnit
    private EntityManagerFactory emf;
    
    public void addSpitter(Spitter spitter){
        emf.createEntityManager().persist(spitter);
    }
    
    public Spitter getSpitterById(long id){
        return emf.createEntityManger().find(Spitter.class,id);
    }
    
    public void saveSpitter(Spitter spitter){
        emf.createEntityManger().merge(spitter);
    }
...
}
```

程序清单11.2中，需要注意的是EntityManagerFactory属性，它使用了@PersistenceUnit注解，因此，Spring会将EntityManagerFactory注入到Repository之中。有了EntityManagerFactory之后，JpaSpitterRepository的方法就能使用它来创建EntityManager了，然后EntityManager可以针对数据库执行操作。

在JpaSpitterRepository中，唯一的问题在于每个方法都会调用createEntityManager()。除了引入易出错的重复代码以外，这还意味着每次调用Repository的方法时，都会创建一个新的EntityManager。这种复杂性源于事务。如果我们能够预先准备好EntityManager，那会不会更加方便呢？

这里的问题在于EntityManager并不是线程安全的，一般来讲并不适合注入到像Repository这样共享的单例bean中。但是，这并不意味着我们没有办法要求注入EntityManager。如下的程序清单展现了如何借助@PersistentContext注解为JpaSpitterRepository设置EntityManager。

## 程序清单11.3　将EntityManager的代理注入到Repository之中

```java
package com.habuma.spittr.persistence;
import java.util.List;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import org.springframework.dao.DataAccessException;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;
import com.habuma.spittr.domain.Spitter;
import com.habuma.spittr.domain.Spittle;

@Repository
@Transactional
public class JpaSpitterRepository implements SpitterRepository {
    @PersistenceContext
    private EntityManger em;//注入EntityManager
    
    public void addSpitter(Spitter spitter){
        em.persist(spitter);//使用EntityManager
    }
    
    public Spitter getSpitterById(long id){
        return em.find(Spitter.class,id);
    }
    
    public void saveSpitter(Spitter spitter){
        em.merge(spitter);
    }
...
}
```

在这个新版本的JpaSpitterRepository中，直接为其设置了EntityManager，这样的话，在每个方法中就没有必要再通过EntityManagerFactory创建EntityManager了。尽管这种方式非常便利，但是你可能会担心注入的EntityManager会有线程安全性的问题。

这里的真相是@PersistenceContext并不会真正注入EntityManager——至少，精确来讲不是这样的。它没有将真正的EntityManager设置给Repository，而是给了它一个EntityManager的代理。真正的EntityManager是与当前事务相关联的那一个，如果不存在这样的EntityManager的话，就会创建一个新的。这样的话，我们就能始终以线程安全的方式使用实体管理器。

另外，还需要了解@PersistenceUnit和@PersistenceContext并不是Spring的注解，它们是由JPA规范提供的。为了让Spring理解这些注解，并注入EntityManager Factory或EntityManager，我们必须要配置Spring的Persistence-AnnotationBeanPostProcessor。如果你已经使用了<context:annotation-config>或<context:component-scan>，那么你就不必再担心了，因为这些配置元素会自动注册PersistenceAnnotationBeanPostProcessor bean。否则的话，我们需要显式地注册这个bean：

```java
@Bean
public PersistenceAnnotationBeanPostProcessor paPostProcessor(){
    return new PersistenceAnnotationBeanPostProcessor();
}
```

你可能也注意到了JpaSpitterRepository使用了@Repository和@Transactional注解。@Transactional表明这个Repository中的持久化方法是在事务上下文中执行的。

对于@Repository注解，它的作用与开发Hibernate上下文Session版本的Repository时是一致的。由于没有使用模板类来处理异常，所以我们需要为Repository添加@Repository注解，这样PersistenceExceptionTranslationPostProcessor就会知道要将这个bean产生的异常转换成Spring的统一数据访问异常。
既然提到了PersistenceExceptionTranslationPostProcessor，要记住的是我们需要将其作为一个bean装配到Spring中，就像我们在Hibernate样例中所做的那样：

```java
@Bean
public BeanPostProcessor persistenceTranslation(){
    return new PersitenceExceptionTranslationPostProcessor();
}
```

提醒一下，不管对于JPA还是Hibernate，异常转换都不是强制要求的。如果你希望在Repository中抛出特定的JPA或Hibernate异常，只需将PersistenceException-TranslationPostProcessor省略掉即可，这样原来的异常就会正常地处理。但是，如果使用了Spring的异常转换，你会将所有的数据访问异常置于Spring的体系之下，这样以后切换持久化机制的话会更容易。

## 11.3　借助Spring Data实现自动化的JPA Repository

尽管程序清单11.2和11.3程序清单中的方法都很简单，但它们依然还会直接与EntityManager交互来查询数据库。并且，仔细看一下的话，这些代码多少还是样板式的。例如，让我们重新审视addSpitter()方法：

```java
public void addSpitter(Spitter spitter){
    entityManager.persist(spitter);
}
```

在任何具有一定规模的应用中，你可能会以几乎完全相同的方式多次编写这种方法。实际上，除了所持久化的Spitter对象不同以外，我敢打赌你以前肯定写过类似的方法。其实，JpaSpitterRepository中的其他方法也没有什么太大的创造性。领域对象会有所不同，但是所有Repository中的方法都是很通用的。

为什么我们需要一遍遍地编写相同的持久化方法呢，难道仅仅是因为要处理的领域类型不同吗？Spring Data JPA能够终结这种样板式的愚蠢行为。我们不再需要一遍遍地编写相同的Repository实现，Spring Data能够让我们只编写Repository接口就可以了。根本就不再需要实现类了。

例如，看一下SpitterRepository接口。

### 程序清单11.4　借助Spring Data，以接口定义的方式创建Repository

```java
public interface SpitterRepository extends JpaRepository<Spitter,Long>{
}
```

此时，SpitterRepository看上去并没有什么作用。但是，它的功能远超出了表面上所看到的那样。

编写Spring Data JPA Repository的关键在于要从一组接口中挑选一个进行扩展。这里，SpitterRepository扩展了Spring Data JPA的JpaRepository（稍后，我会介绍几个其他的接口）。通过这种方式，JpaRepository进行了参数化，所以它就能知道这是一个用来持久化Spitter对象的Repository，并且Spitter的ID类型为Long。另外，它还会继承18个执行持久化操作的通用方法，如保存Spitter、删除Spitter以及根据ID查询Spitter。

此时，你可能会想下一步就该编写一个类实现SpitterRepository和它的18个方法了。如果真的是这样的话，那本章就会变得乏味无聊了。其实，我们根本不需要编写SpitterRepository的任何实现类，相反，我们让Spring Data来为我们做这件事请。我们所需要做的就是对它提出要求。

为了要求Spring Data创建SpitterRepository的实现，我们需要在Spring配置中添加一个元素。如下的程序清单展现了在XML配置中启用Spring Data JPA所需要添加的内容：

### 程序清单11.5　配置Spring Data JPA

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jpa="http://www.springframework.org/schema/data/jpa"
    xsi:schemaLocation="http://www.springframework.org/schema/data/jpa
        http://www.springframework.org/schema/data/jpa/spring-jpa-1.0.xsd">
        <jpa:repositories base-package="com.habuma.spittr.db" />
    ...
```

<jpa:repositories>元素掌握了Spring Data JPA的所有魔力。就像<context:component-scan>元素一样，<jpa:repositories>元素也需要指定一个要进行扫描的base-package。不过，<context:component-scan>会扫描包（及其子包）来查找带有@Component注解的类，而<jpa:repositories>会扫描它的基础包来查找扩展自Spring Data JPA Repository接口的所有接口。如果发现了扩展自Repository的接口，它会自动生成（在应用启动的时候）这个接口的实现。

如果要使用Java配置的话，那就不需要使用<jpa:repositories>元素了，而是要在Java配置类上添加@EnableJpaRepositories注解。如下就是一个Java配置类，它使用了@EnableJpaRepositories注解，并且会扫描com.habuma.spittr.db包：

```java
@Configuration
@EnableJpaRepositories(basePackages="com.habuma.spittr.db")
public class JpaConfiguration{
    ...
}
```

让我们回到SpitterRepository接口，它扩展自JpaRepository，而JpaRepository又扩展自Repository标记接口（虽然是间接的）。因此，SpitterRepository就传递性地扩展了Repository接口，也就是Repository扫描时所要查找的接口。当Spring Data找到它后，就会创建SpitterRepository的实现类，其中包含了继承自JpaRepository、PagingAndSortingRepository和CrudRepository的18个方法。

很重要的一点在于Repository的实现类是在应用启动的时候生成的，也就是Spring的应用上下文创建的时候。它并不是在构建时通过代码生成技术产生的，也不是接口方法调用时才创建的。

很漂亮的技术，对吧？

Spring Data JPA很棒的一点在于它能为Spitter对象提供18个便利的方法来进行通用的JPA操作，而无需你编写任何持久化代码。但是，如果你的需求超过了它所提供的这18个方法的话，该怎么办呢？幸好，Spring Data JPA提供了几种方式来为Repository添加自定义的方法。让我们看一下如何为Spring Data JPA编写自定义的查询方法。

#### 11.3.1　定义查询方法

现在，SpitterRepository需要完成的一项功能是根据给定的username查找Spitter对象。比如，我们将SpitterRepository接口修改为如下所示的样子：

```java
public interface SpitterRepository extends JpaRepository<Spitter,Long>{
    Spitter findByUsername(String username);
}
```

这个新的findByUserName()非常简单，但是足以满足我们的需求。现在，该如何让Spring Data JPA提供这个方法的实现呢？

实际上，我们并不需要实现findByUsername()。方法签名已经告诉Spring Data JPA足够的信息来创建这个方法的实现了。

当创建Repository实现的时候，Spring Data会检查Repository接口的所有方法，解析方法的名称，并基于被持久化的对象来试图推测方法的目的。本质上，Spring Data定义了一组小型的领域特定语言（domain-specific language ，DSL），在这里，持久化的细节都是通过Repository方法的签名来描述的。

Spring Data能够知道这个方法是要查找Spitter的，因为我们使用Spitter对JpaRepository进行了参数化。方法名findByUsername确定该方法需要根据username属性相匹配来查找Spitter，而username是作为参数传递到方法中来的。另外，因为在方法签名中定义了该方法要返回一个Spitter对象，而不是一个集合，因此它只会查找一个username属性匹配的Spitter。

findByUsername()方法非常简单，但是Spring Data也能处理更加有意思的方法名称。Repository方法是由一个动词、一个可选的主题（Subject）、关键词By以及一个断言所组成。在findByUsername()这个样例中，动词是find，断言是Username，主题并没有指定，暗含的主题是Spitter。

作为编写Repository方法名称的样例，我们参照名为readSpitterByFirstname-OrLastname()的方法，看一下方法中的各个部分是如何映射的。图11.1展现了这个方法是如何拆分的。

我们可以看到，这里的动词是read，与之前样例中的find有所差别。Spring Data允许在方法名中使用四种动词：get、read、find和count。其中，动词get、read和find是同义的，这三个动词对应的Repository方法都会查询数据并返回对象。而动词count则会返回匹配对象的数量，而不是对象本身。

![image](BA8453CF9F394501AD0E8B55813F8107)


图11.1　Repository方法的命名遵循一种模式，有助于Spring Data
生成针对数据库的查询

Repository方法的主题是可选的。它的主要目的是让你在命名方法的时候，有更多的灵活性。如果你更愿意将方法称为readSpittersByFirstnameOrLastname()而不是 readByFirstnameOrLastname()的话，那么你尽可以这么做。

对于大部分场景来说，主题会被省略掉。readSpittersByFirstnameOrLastname()与readPuppiesByFirstnameOrLastname()并没有什么差别，它们与readThose ThingsWeWantByFirstnameOrLastname()同样没有什么区别。要查询的对象类型是通过如何参数化JpaRepository接口来确定的，而不是方法名称中的主题。

在省略主题的时候，有一种例外情况。如果主题的名称以Distinct开头的话，那么在生成查询的时候会确保所返回结果集中不包含重复记录。

断言是方法名称中最为有意思的部分，它指定了限制结果集的属性。在readByFirstnameOrLastname()这个样例中，会通过firstname属性或lastname属性的值来限制结果。

在断言中，会有一个或多个限制结果的条件。每个条件必须引用一个属性，并且还可以指定一种比较操作。如果省略比较操作符的话，那么这暗指是一种相等比较操作。不过，我们也可以选择其他的比较操作，包括如下的种类：

 
- IsAfter、After、IsGreaterThan、GreaterThan
- IsGreaterThanEqual、GreaterThanEqual
- IsBefore、Before、IsLessThan、LessThan
- IsLessThanEqual、LessThanEqual
- IsBetween、Between
- IsNull、Null
- IsNotNull、NotNull
- IsIn、In
- IsNotIn、NotIn
- IsStartingWith、StartingWith、StartsWith
- IsEndingWith、EndingWith、EndsWith
- IsContaining、Containing、Contains
- IsLike、Like
- IsNotLike、NotLike
- IsTrue、True
- IsFalse、False
- Is、Equals
- IsNot、Not

要对比的属性值就是方法的参数。完整的方法签名如下所示：

```java
List<Spitter> readByFirstnameOrLastname(string first,String last);
```


要处理String类型的属性时，条件中可能还会包含IgnoringCase或IgnoresCase，这样在执行对比的时候就会不再考虑字符是大写还是小写。例如，要在firstname和lastname属性上忽略大小写，那么可以将方法签名改成如下的形式：

```java
List<Spitter> readByFirstnameIgnoringCaseOrLastnameIgnoresCase(String first,String last);
```

需要注意，IgnoringCase和IgnoresCase是同义的，你可以随意挑选一个最合适的。

作为IgnoringCase/IgnoresCase的替代方案，我们还可以在所有条件的后面添加AllIgnoringCase或AllIgnoresCase，这样它就会忽略所有条件的大小写：

```java
List<Spitter> readByFirstnameOrLastnameAllIgnoresCase(String first,String last);
```

注意，参数的名称是无关紧要的，但是它们的顺序必须要与方法名称中的操作符相匹配。

最后，我们还可以在方法名称的结尾处添加OrderBy，实现结果集排序。例如，我们可以按照lastname属性升序排列结果集：

```java
List<Spitter> readByFirstnameOrLastnameOrderByLastnameAsc(String first,String last);
```

如果要根据多个属性排序的话，只需将其依序添加到OrderBy中即可。例如，下面的样例中，首先会根据lastname升序排列，然后根据firstname属性降序排列：

```java
List<Spitter> readByFirstnameOrLastnameOrderByLastnameAscFirstnameDesc(String first,String last);
```

可以看到，条件部分是通过And或者Or进行分割的。

我们不可能（至少很难）提供一个权威的列表，将使用Spring Data方法命名约定可以编写出来的方法种类全部列出来。但是，如下给出了几个符合方法命名约定的方法签名：

- List<Pet> findPetsByBreedIn(List<String> breed)
- int countProductsByDiscontinuedTrue()
- List<Order> findByShippingDateBetween(Date start, Date end)

我们只是初步体验了所能声明的方法种类，Spring Data JPA会为我们实现这些方法。现在，我们只需知道通过使用属性名和关键字构建Repository方法签名，就能让Spring Data JPA生成方法实现，完成几乎所有能够想象到的查询。

不过，Spring Data这个小型的DSL依旧有其局限性，有时候通过方法名称表达预期的查询很烦琐，甚至无法实现。如果遇到这种情形的话，Spring Data能够让我们通过@Query注解来解决问题。

### 11.3.2　声明自定义查询

假设我们想要创建一个Repository方法，用来查找E-mail地址是Gmail邮箱的Spitter。有一种方式就是定义一个findByEmailLike()方法，然后每次想查找Gmail用户的时候就将“%gmail.com”传递进来。不过，更好的方案是定义一个更加便利的findAllGmailSpitters()方法，这样的话，就不用将Email地址的一部分传递进来了：

```java
List<Spitter> findAllGmailSpitters();
```

不过，这个方法并不符合Spring Data的方法命名约定。当Spring Data试图生成这个方法的实现时，无法将方法名的内容与Spitter元模型进行匹配，因此会抛出异常。

如果所需的数据无法通过方法名称进行恰当地描述，那么我们可以使用@Query注解，为Spring Data提供要执行的查询。对于findAllGmailSpitters()方法，我们可以按照如下的方式来使用@Query注解：

```java
@Query("select s from Spitter s where s.email like '%gmail.com'")
List<Spitter> findAllGmailSpitters();
```

我们依然不需要编写findAllGmailSpitters()方法的实现，只需提供查询即可，让Spring Data JPA知道如何实现这个方法。

可以看到，当使用方法命名约定很难表达预期的查询时，@Query注解能够发挥作用。如果按照命名约定，方法的名称特别长的时候，也可以使用这个注解。例如，考虑如下的查询方法：

```java
List<Order> findByCustomerAddressZipCodeOrCustomerNameAndCustomerAddressState();
```

这真的是一个方法的名称！我不得不在返回类型后将其断开，这样才能适应本书页面的宽度。

我承认这是一个有点牵强的例子。但在现实世界中，确实存在这样的需求，使用Repository方法所执行的查询会得到一个很长的方法名。在这种情况下，你最好使用一个较短的方法名，并使用@Query来指定该方法要如何查询数据库。

对于Spring Data JPA的接口来说，@Query是一种添加自定义查询的便利方式。但是，它仅限于单个JPA查询。如果我们需要更为复杂的功能，无法在一个简单的查询中处理的话，该怎么办呢？

### 11.3.3　混合自定义的功能

有些时候，我们需要Repository所提供的功能是无法用Spring Data的方法命名约定来描述的，甚至无法用@Query注解设置查询来实现。尽管Spring Data JPA非常棒，但是它依然有其局限性，可能需要我们按照传统的方式来编写Repository方法：也就是直接使用EntityManager。当遇到这种情况的时候，我们是不是要放弃Spring Data JPA，重新按照11.2.2小节中的方式来编写Repository呢？

简单来说，是这样的。如果你需要做的事情无法通过Spring Data JPA来实现，那就必须要在一个比Spring Data JPA更低的层级上使用JPA。好消息是我们没有必要完全放弃Spring Data JPA。我们只需在必须使用较低层级JPA的方法上，才使用这种传统的方式即可，而对于Spring Data JPA知道该如何处理的功能，我们依然可以通过它来实现。

当Spring Data JPA为Repository接口生成实现的时候，它还会查找名字与接口相同，并且添加了Impl后缀的一个类。如果这个类存在的话，Spring Data JPA将会把它的方法与Spring Data JPA所生成的方法合并在一起。对于SpitterRepository接口而言，要查找的类名为SpitterRepositoryImpl。

为了阐述该功能，假设我们需要在SpitterRepository中添加一个方法，发表Spittle数量在10,000及以上的Spitter将会更新为Elite状态。使用Spring Data JPA的方法命名约定或使用@Query均没有办法声明这样的方法。最为可行的方案是使用如下的eliteSweep()方法。

### 程序清单11.6　将活跃的Spitter用户升级为Elite状态的Repository方法

```java
public class SpitterRepositoryImpl implements SpitterSweeper{
    @PersistenceContext
    private EntityManager em;
    
    public int eliteSweep(){
        String update = 
            "UPDATE SPitter spitter " +
            "SET spitter.status = 'Elite' " +
            "WHERE spitter.status = 'Newbie' " +
            "AND spitter.id IN (" +
            "SELECT s FROM SPitter s WHERE (" +
            " SELECT COUNT(spittles) FROM s.spittles spittles) > 10000" +
            ")";
        return em.createQuery(update).executeUpdate();
    }
}
```

我们可以看到，eliteSweep()方法与之前在11.2.2小节中所创建的Repository方法并没有太大的差别。SpitterRepositoryImpl没有什么特殊之处，它使用被注入的EntityManager来完成预期的任务。

注意，SpitterRepositoryImpl并没有实现SpitterRepository接口。Spring Data JPA负责实现这个接口。SpitterRepositoryImpl（将它与Spring Data的Repository关联起来的是它的名字）实现了SpitterSweeper接口，它如下所示：

```java
public interface SpitterSweeper{
    int.eliteSweep();
}
```

我们还需要确保eliteSweep()方法会被声明在SpitterRepository接口中。要实现这一点，避免代码重复的简单方式就是修改SpitterRepository，让它扩展SpitterSweeper：

```java
public interface SpitterRepository extends JpaRepository<Spitter, Long>,SpitterSweeper{
    ....
}
```

如前所述，Spring Data JPA将实现类与接口关联起来是基于接口的名称。但是，Impl后缀只是默认的做法，如果你想使用其他后缀的话，只需在配置@EnableJpa-Repositories的时候，设置repositoryImplementationPostfix属性即可：

```java
@EnableJpaRepositoryies(
    basePackages="com.habuma.spittr.db",
    repositoryImplementationPostfix="Helper")
```

如果在XML中使用<jpa:repositories>元素来配置Spring Data JPA的话，我们可以借助repository-impl-postfix属性指定后缀：

```xml
<jpa:repositories base-package="com.habuma.spittr.db"
    repository-impl-postfix="Helper" />
```

我们将后缀设置成了Helper，Spring Data JPA将会查找名为SpitterRepository-Helper的类，用它来匹配SpitterRepository接口。

11.4　小结

对于很多应用来讲，关系型数据库是主流的数据存储形式，并且这种情况已经持续了很多年。使用JDBC并且将对象映射为数据库表是很烦琐乏味的事情，像Hibernate和JPA这样的ORM方案能够让我们以更加声明式的模型实现数据持久化。尽管Spring没有为ORM提供直接的支持，但是它能够与多种流行的ORM方案集成，包括Hibernate与Java持久化API。

在本章中，我们看到了如何在Spring应用中使用Hibernate的上下文Session，这样我们的Repository就能包含很少甚至不包含Spring相关的代码。与之类似，我们还看到了如何将EntityManagerFactory或EntityManager注入到Repository实现中，从而实现不依赖于Spring的JPA Repository。

我们稍后初步了解了Spring Data，在这个过程中，只需声明JPA Repository接口即可，让Spring Data JPA在运行时自动生成这些接口的实现。当我们需要的Repository方法超出了Spring Data JPA所提供的功能时，可以借助@Query注解以及编写自定义的Repository方法来实现。

但是，对于Spring Data的整体功能来说，我们只是接触到了皮毛。在下一章中，我们将会更加深入地学习Spring Data的方法命名DSL，以及Spring Data如何为关系型数据库以外的领域带来帮助。也就是说：我们将会看到Spring Data如何支持新兴的NoSQL数据库，这些数据库在最近几年变得越来越流行。