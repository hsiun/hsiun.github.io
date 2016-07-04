---
title: 使用Spring处理一个RESTful请求
date: 2016-06-28 12:11:00
tags: Java开发
---


这里介绍的是用Spring来发起一个http请求，请求的对方响应一个json数据，这里主要介绍对这个请求的发起和处理的过程。

环境
java1.8
Ecplise
Maven3

下面是主要的代码，首先是Maven的配置文件：

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.gaospot</groupId>
  <artifactId>consum</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.3.5.RELEASE</version>
  </parent>
  
  <properties>
    <java.version>1.8</java.version>
  </properties>
  
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
    </dependency>
    
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
    </dependency>
  </dependencies>
  
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

然后新建两个bean类，用来响应json请求。

```
package consum;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true) //只处理json格式的，不符合此格式的忽略
public class Quote {
  
  private String type;
  private Value value;
  
  public Quote() {}
  
  public String getType() {
    return type;
  }

  public void setType(String type) {
    this.type = type;
  }
  
  public Value getValue() {
    return value;
  }
  
  public void setValue(Value value) {
    this.value = value;
  }
  
  @Override
  public String toString() {
    return "Quote{" +
        "type='" + type + '\'' +
        ", value=" + value +
        '}';
  }
}

```

```
package consum;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class Value {
  
  private Long id;
  private String quote;
  
  public Value() {}
  
  public Long getId() {
    return id;
  }
  
  public String getQuote() {
    return quote;
  }
  
  public void setId(Long id) {
    this.id = id;
  }
  
  public void setQuote(String quote) {
    this.quote = quote;
  }
  
  @Override
  public String toString() {
    return "Value{" +
        "id=" + id +
        ", quote='" + quote + '\'' +
        '}';
  }

}

```

在Application类中调用我们的请求类。

```
package consum;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class Application implements CommandLineRunner {
  
  private static final Logger log = LoggerFactory.getLogger(Application.class);
  
  public static void main(String args[]) {
    SpringApplication.run(Application.class);
  }

  @Override
  public void run(String... arg0) throws Exception {
    RestTemplate restTemplate = new RestTemplate();
    Quote quote = restTemplate.getForObject("http://gturnquist-quoters.cfapps.io/api/random", 
          Quote.class);   //restTemplate方法会使用jackson的json库将json转换为quote对象
    log.info(quote.toString()); //将转换后的内容记录到日志，info级别的日志将在控制台输出
    
  }
  

}

```