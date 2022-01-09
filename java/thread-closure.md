# 线程封闭

通过一个 Spring Boot 的 Maven 项目来举例，如何使用`java.lang.ThreadLocal<T>`来实现线程变量的封闭存储

## Maven 配置

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.2</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>training</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <name>training</name>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
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
</project>
```

## ThreadLocal

创建一个基础的包装类，来将变量存储在单个线程中。在 ThreadLocal 是通过一个 Map 依据线程的 ID 来存储不同线程的变量，以实现隔离不同线程之间的数据

```java
package com.example.training.common;

public class RequestHolder {
    private static final ThreadLocal<Long> REQUEST_HOLDER = new ThreadLocal<>();

    public static void add(Long id) {
        REQUEST_HOLDER.set(id);
    }

    public static Long getId() {
        return REQUEST_HOLDER.get();
    }

    public static void remove() {
        REQUEST_HOLDER.remove();
    }
}
```

## 过滤器

添加一个过滤器，在过滤器中将变量存入 ThreadLocal 中

```java
package com.example.training.filter;

import com.example.training.common.RequestHolder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

public class HttpFilter implements Filter {

    private static final Logger log = LoggerFactory.getLogger(HttpFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws ServletException, IOException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;
        log.info("doFilter Thread ID {}, Servlet Path {}", Thread.currentThread().getId(), request.getServletPath());
        RequestHolder.add(Thread.currentThread().getId());
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }
}
```

## 拦截器

添加一个拦截器，在请求完成后将存储的变量占用的内存空间释放

```java
package com.example.training.filter;

import com.example.training.common.RequestHolder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HttpInterceptor implements HandlerInterceptor {
    private static final Logger log = LoggerFactory.getLogger(HttpInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        log.info("preHandle {}", request.getServletPath());
        return Boolean.TRUE;
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        RequestHolder.remove();
        log.info("afterCompletion {}", request.getServletPath());
    }
}
```

## 控制器

然后创建一个控制器，模拟一个用户请求

```java
package com.example.training.controller;

import com.example.training.common.RequestHolder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import javax.servlet.http.HttpServletRequest;
import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping("/threadLocal")
public class ThreadLocalController {
    private static final Logger log = LoggerFactory.getLogger(ThreadLocalController.class);

    @RequestMapping(path = "/test", method = {RequestMethod.GET, RequestMethod.POST})
    public Map<String, Long> test(HttpServletRequest request) {
        log.info("ThreadLocalController {}", request.getServletPath());
        final Map<String, Long> map = new HashMap<>();
        map.put("threadId", RequestHolder.getId());
        return map;
    }
}
```

## 启动类

最后是 Spring Boot 的启动类

```java
package com.example.training;

import com.example.training.filter.HttpFilter;
import com.example.training.filter.HttpInterceptor;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@SpringBootApplication
public class TrainingApplication implements WebMvcConfigurer {

    public static void main(String[] args) {
        SpringApplication.run(TrainingApplication.class, args);
    }

    @Bean
    public FilterRegistrationBean<HttpFilter> httpFilter() {
        final FilterRegistrationBean<HttpFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new HttpFilter());
        registrationBean.addUrlPatterns("/threadLocal/*");
        return registrationBean;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HttpInterceptor()).addPathPatterns("/**");
    }
}
```

## 模拟请求

模拟一个测试请求

```bash
curl -i http://localhost:8080/threadLocal/test
```

返回信息类似

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    15    0    15    0     0    161      0 --:--:-- --:--:-- --:--:--   163HTTP/1.1 200
Content-Type: application/json
Transfer-Encoding: chunked
Date: Sun, 09 Jan 2022 09:33:09 GMT

{"threadId":28}
```

Last Modified 2022-01-09
