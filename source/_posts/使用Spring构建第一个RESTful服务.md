---
title: 使用Spring构建第一个RESTful服务
date: 2016-06-24 23:11:00
tags: Java开发
---


最近打算重新学学java，突然发现RESTful这个东西好像蛮有趣的，找来资料学习，找到Spring官方网站的一些资料，这里就跟着官方网站的例子试验了下。记录下实践的过程，一则稳固记忆，二则温故知新。

REST (Representational State Transfer)描述了一个架构样式的网络系统。REST的基本原理就是使用URI来描述资源，每个具体的资源在服务中都对应到一个URI中，数据的传输交流通过json或者是xml来实现，对资源的操作通过http中的方法来实现。RESTful是通过REST造出来的一个形容词，应该说没有具体的定义，他说描述的是一个规范或者说一系列的原则，符合这些规范和原则的我们就把他成为是RESTful。

下面我们就来构建我们的第一个RESTful的Web服务，下面是开发相关的一些信息：
开发环境：Eclipse+Maven插件
框架：Spring
流程管理：Maven
Java版本：JDK1.8+

最后实现的效果就是当我们访问如http://localhost:8080/greeting这样的地址时会返回。如下所示的一个json字符串。

```
{"id":1,"content":"Hello, World!"}
```
这里的这个id是一个可以增长计数的一个ID，每一次访问的时候这个ID都会增长。


1，在Eclipse（要已经安装了Maven插件）中新建一个Maven项目，这里格式选择jar，最好将项目编译为一个jar包，这样可以在其他任意地方使用。其他的随便填。

2，编写Maven的配置文件poe.xml，文件内容如下。

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  
  <groupId>com.gaospot</groupId>
  <artifactId>hello</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  
  <parent>
  	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-parent</artifactId>
  	<version>1.3.5.RELEASE</version>
  </parent>
  
  <dependencies>
  	<dependency>
  		<groupId>org.springframework.boot</groupId>
  		<artifactId>spring-boot-starter-web</artifactId>
  	</dependency>
  	<dependency>
  		<groupId>org.springframework.boot</groupId>
  		<artifactId>spring-boot-starter-test</artifactId>
  		<scope>test</scope>
  	</dependency>
  	<dependency>
  		<groupId>com.jayway.jsonpath</groupId>
  		<artifactId>json-path</artifactId>
  		<scope>test</scope>
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
  
  <repositories>
  	<repository>
  		<id>spring-releases</id>
  		<url>https://repo.spring.io/libs-release</url>
  	</repository>
  </repositories>
  
  <pluginRepositories>
  	<pluginRepository>
  		<id>spring-releases</id>
  		<url>https://repo.spring.io/libs-release</url>
  	</pluginRepository>
  </pluginRepositories>
  
</project>
```

dependencies引入的是这个项目所需要依耐的软件包。repositories指定这些软件包的下载地址。parent指出的是父模块，没搞明白这里为什么要使用这个参数，这里应该是只需要dependencies中指定了依耐就可以的。

3、在源代码目录中新建一个包，在包里创建Bean，Control和Application，Application在这里是一个含有主函数的类，可以直接运行这个类来测试我们的应用。

```
package hello;

public class Greeting {

	private final long id;
	private final String content;
	
	public Greeting(long id, String content) {
		this.id = id;
		this.content = content;
	}
	
	public long getId() {
		return id;
	}
	
	public String getContent() {
		return content;
	}
}

```

```
package hello;

import java.util.concurrent.atomic.AtomicLong;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class GreetingController {
	
	private static final String template = "Hello, %s!";
	private final AtomicLong counter = new AtomicLong();
	
	@RequestMapping("/greeping")
	public Greeting greeting(@RequestParam(value="name", defaultValue="World")String name) {
		return new Greeting(counter.incrementAndGet(),
				String.format(template, name));
	}

}

```

```
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}

```

4、现在源码都已经编写好了，可以测试运用了，运用运行后在浏览器中访问之间的那个地址就可以得到我们想要的结果了。