---
title: SprinBoot2.x 整合 Redission (分布式锁)
date: 2020/06/15 17:59:44

tags: 
 - Java
 - SpringBoot
 - Redis

categories: 
 - Redis
---

### 源码Demo 

https://github.com/wanglei0530/redis_lock.git

### 项目结构

![1](https://tva1.sinaimg.cn/large/007S8ZIlly1ggqie2jn1tj30km0rogo7.jpg)

### Pom依赖

```pom
<dependencies>
  
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
  
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
  
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.49</version>
    </dependency>
  
    <dependency>
      <groupId>org.redisson</groupId>
      <artifactId>redisson</artifactId>
      <version>3.5.0</version>
    </dependency>
  
  
    <dependency>
      <groupId>cn.hutool</groupId>
      <artifactId>hutool-all</artifactId>
      <version>4.6.0</version>
    </dependency>
    
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
      <exclusions>
        <exclusion>
          <groupId>org.junit.vintage</groupId>
          <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
  </dependencies>
```

### Yml

```yml
server:
  port: 8000

spring:
  redis:
#    cluster:
#      nodes: 127.0.0.1:6379,127.0.0.1:6379
    database: 1
    host: localhost
    port: 6379
    password:
    jedis:
      pool:
        max-idle: 100
        max-wait: -1s
        max-active: 1000

```

### RedissionConfig

```java
package in.nanopay.redis_lock.config;

import cn.hutool.core.util.StrUtil;
import lombok.extern.slf4j.Slf4j;
import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.config.Config;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import java.io.IOException;

/**
 * @author ：wanglei
 * @create ：2020-07-13 15:49
 * @description：
 */
@Component
@Configuration
@Slf4j
public class RedissonConfig {

	@Autowired
	private Environment env;

	@Bean
	public RedissonClient getRedisson() throws IOException {
		String host = env.getProperty("spring.redis.host");
		String nodes = env.getProperty("spring.redis.cluster.nodes");
		RedissonClient redisson = null;
		Config config = null;
		if (StrUtil.isNotBlank(nodes)) {
			config = cluster(nodes);
		} else if (StrUtil.isNotBlank(host)) {
			config = single(host);
		}
		redisson = Redisson.create(config);
		log.debug("redisson config" + redisson.getConfig().toJSON());
		return redisson;
	}

	/**
	 * 集群模式 (redisson版本是3.5，集群的ip前面要加上“redis://”，不然会报错，3.2版本可不加)
	 *
	 * @return
	 */
	private Config cluster(String configStr) {
		String[] nodes = configStr.split(",");
		for (int i = 0; i < nodes.length; i++) {
			nodes[i] = "redis://" + nodes[i];
		}
		Config config = new Config();
		config.useClusterServers() //这是用的集群server
				.setScanInterval(2000) //设置集群状态扫描时间
				.addNodeAddress(nodes)
				.setRetryAttempts(3) //重试次数
		;
		return config;
	}

	/**
	 * 单机模式
	 *
	 * @return
	 */
	private Config single(String configStr) {
		String port = env.getProperty("spring.redis.port");
		String database = env.getProperty("spring.redis.database");
		String addr = new StringBuffer("redis://")
				.append(configStr)
				.append(":")
				.append(port)
				.toString();

		Config config = new Config();
		config.useSingleServer()
				.setAddress(addr)
				.setDatabase(Integer.parseInt(database))
		;
		return config;
	}
}

```

### LockTestController

```java

	@GetMapping("/many")
	public String many(@Param("productId") String productId, @Param("qty") Integer qty) {
		RLock redissonLock = redissLockUtil.lock(productId, 10);
		try {
			int stock = getStock();
			if (stock <= 0) {
				return "没有库存了！";
			}
			int realstock = stock - qty;
			redisTemplate.opsForValue().set("stock", realstock + "");
			log.info(DateUtil.format("--- 剩余库存: " + realstock);
			return "" + getStock();
		} finally {
			redissonLock.unlock();
		}
	}

    private int getStock() {
		return Integer.parseInt(redisTemplate.opsForValue().get("stock"));
	}

```

### nginx 负载配置

```
    upstream redislock{
        server 10.247.34.208:8000;
        server 10.247.34.208:8001;
    }

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;
        #access_log  logs/host.access.log  main;
        location / {
            root   html;
            index  index.html index.htm;
            proxy_pass http://redislock;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

```