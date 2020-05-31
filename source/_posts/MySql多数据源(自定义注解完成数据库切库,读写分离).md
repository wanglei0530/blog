---
title: MySql多数据源(自定义注解完成数据库切库,读写分离)
date: 2019/01/04 17:59:44
---

### 本文参照：

前提

​		这几天，学员们反馈希望学习一下自定义注解，正好准备高并发课程内容里有一块涉及到使用自定义注解完成数据库切库的内容。这里单独写一篇文章记录说明一下。

为什么会有数据库切库一说

首先，许多项目都有主库与从库，有的主库后面甚至会有很多个从库，主从库之间的通常同步也很快，这为数据库切库提供了一个基础，因为可以去不同的数据库查询，得到相同的结果（如果不同的数据库是完全不同的，这个不在我们这篇文章讨论的范围之内，那个属于让项目支持多个数据源）

其次，随着项目越来越大、操作的用户越来越多，对数据库的请求操作越来越多，很容易想到的是将读写请求分开，将写请求交给主库处理，读请求直接从某个从库读取。这样可以极大的减少大量对主库的请求，提升主库的性能。

接下来具体说一下如何通过自定义注解完成切库（代码使用springboot实现）：

### 第一步、定义我们自己的切库注解类

自定义注解有几点需要注意：

@Target 是作用的目标，接口、方法、类、字段、包等等，具体看：ElementType

2）@Retention 是注解存在的范围，RUNTIME代表的是注解会在class字节码文件中存在，在运行时可以通过反射获取到，具体看：RetentionPolicy

3）允许的变量，通常都要给定默认值，比如我们使用一个service时，可以@Service，也可以@Service("xxxx")

@Retention(RetentionPolicy.RUNTIME) @Target({ElementType.METHOD,ElementType.TYPE}) public @interface RoutingDataSource {     String value() default DataSources.MASTER_DB; }

### 第二步、定义需要使用的数据库及配置

#### 1、数据库配置：application.properties，这里要注意不同db的前缀区别

```yaml
datasource:
  druid:
    db-master:
      username: XXXXXX
      password: XXXXXX!@#
      url: jdbc:mysql://XXXXXX/dev
      db-type: com.alibaba.druid.pool.DruidDataSource
      driver-class-name:  com.mysql.cj.jdbc.Driver
    db-slave:
      username: XXXXXX
      password: XXXXXX!@#
      url: jdbc:mysql://XXXXXX:10092/dev
      db-type: com.alibaba.druid.pool.DruidDataSource
      driver-class-name:  com.mysql.cj.jdbc.Driver
```
#### 2、定义支持的数据源id：

```java
public interface DataSources {
    String MASTER_DB = "db-maste";
    String SLAVE_DB = "db-slave";
}
```
#### 3、多数据源配置代码

```java
import com.alibaba.druid.spring.boot.autoconfigure.DruidDataSourceBuilder;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;
@Configuration
@Component
public class DynamicDataSourceConfig {
    @Bean
    @ConfigurationProperties("spring.datasource.druid.wl-master")
    public DataSource  xiaobinMasterDataSource(){
        return DruidDataSourceBuilder.create().build();
    }

    @Bean
    @ConfigurationProperties("spring.datasource.druid.wl-slave")
    public DataSource  xiaobinSlaveDataSource(){
        return DruidDataSourceBuilder.create().build();
    }
    @Bean
    @Primary
    public DynamicDataSource dataSource(DataSource xiaobinMasterDataSource, DataSource xiaobinSlaveDataSource) {
        Map<Object, Object> targetDataSources = new HashMap<>();
        targetDataSources.put("wl-master",xiaobinMasterDataSource);
        targetDataSources.put("wl-slave", xiaobinSlaveDataSource);
        return new DynamicDataSource(xiaobinMasterDataSource, targetDataSources);
    }
}
```
#### 4、配置成动态数据源：

```java
import org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource;
import org.springframework.lang.Nullable;
import javax.sql.DataSource;
import java.util.Map;

public class DynamicDataSource extends AbstractRoutingDataSource {
    private static final ThreadLocal<String> contextHolder = new ThreadLocal<>();

    public DynamicDataSource(DataSource defaultTargetDataSource, Map<Object, Object> targetDataSources) {
        super.setDefaultTargetDataSource(defaultTargetDataSource);
        super.setTargetDataSources(targetDataSources);
        super.afterPropertiesSet();
    }
    @Override
    protected Object determineCurrentLookupKey() {
        return getDataSource();
    }
    public static void setDataSource(String dataSource) {
        contextHolder.set(dataSource);
    }
    public static String getDataSource() {
        return contextHolder.get();
    }
    public static void clearDataSource() {
        contextHolder.remove();
    }
}
```
#### 5、AOP切面类配置

```java
@Aspect
@Component
public class DataSourceAspect {

    @Pointcut("@annotation(com.xiaobin.annotation.DataSource)")
    public void dataSourcePointCut() {
    }

    @Around("dataSourcePointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        MethodSignature signature = (MethodSignature) point.getSignature();
        Method method = signature.getMethod();

        DataSource dataSource = method.getAnnotation(DataSource.class);
        if(dataSource == null){
            DynamicDataSource.setDataSource("wl-master");
        }else {
            DynamicDataSource.setDataSource(dataSource.name());
        }

        try {
            return point.proceed();
        } finally {
            DynamicDataSource.clearDataSource();
        }
    }
}
```
#### 6、启动类配置，不然报错！

```java
@SpringBootApplication(exclude= {DataSourceAutoConfiguration.class})
@MapperScan(basePackages = "com.wl.mapper")
@Import({DynamicDataSourceConfig.class})
public class StartApp {
    public static void main(String[] args) {
        SpringApplication.run(StartApp.class);
    }
}
```