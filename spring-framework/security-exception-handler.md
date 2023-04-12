# Security Exception Handler

## 返回数据结构

```java
package tech.tystnad.infrastructure.jaxrs;

import com.fasterxml.jackson.annotation.JsonProperty;

/**
 * 带编码的实体容器
 * <p>
 * 一般来说REST服务应采用HTTP Status Code带回错误信息编码
 * 但很多前端开发都习惯以JSON-RPC的风格处理异常，所以仍然保留这个编码容器
 * 用于返回给客户端以形式为“{code,message,data}”的对象格式
 */
public class CodedMessage<T> {
    /**
     * 约定的成功标志
     */
    public static final Integer CODE_SUCCESS = 200;
    /**
     * 默认的失败标志，其他失败含义可以自定义
     */
    public static final Integer CODE_DEFAULT_FAILURE = 0;

    private Integer code;
    private String message;
    @JsonProperty(required = true)
    private T data;

    public CodedMessage(Integer code, String message) {
        setCode(code);
        setMessage(message);
    }

    public CodedMessage(Integer code, String message, T data) {
        setCode(code);
        setMessage(message);
        setData(data);
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

## 实现对应的接口

- org.springframework.security.web.access.AccessDeniedHandler
- org.springframework.security.web.AuthenticationEntryPoint

```java
package tech.tystnad.domain.auth.provider;

import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.stereotype.Component;
import tech.tystnad.infrastructure.jaxrs.CodedMessage;

import java.io.IOException;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;

/**
 * 权限不足的异常处理
 * HTTP 403
 */
@Component
public class InnerAccessDeniedHandler implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException {
        response.setStatus(HttpStatus.FORBIDDEN.value());
        // 返回的数据为 json
        response.setContentType(MediaType.APPLICATION_JSON_VALUE);
        // 字符编码 UTF-8
        response.setCharacterEncoding(StandardCharsets.UTF_8.displayName());
        try (PrintWriter writer = response.getWriter()) {
            ObjectMapper o = new ObjectMapper();
            // 写入一个友好的数据返回
            writer.append(o.writeValueAsString(
                    new CodedMessage<>(HttpStatus.FORBIDDEN.value(), accessDeniedException.getMessage())
            ));
            writer.flush();
        }
    }
}
```

```java
package tech.tystnad.domain.auth.provider;

import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;
import tech.tystnad.infrastructure.jaxrs.CodedMessage;

import java.io.IOException;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;


/**
 * 登录失效的异常处理
 * HTTP 401
 */
@Component
public class InnerAuthenticationEntryPoint implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException {
        response.setStatus(HttpStatus.UNAUTHORIZED.value());
        // 返回的数据为 json
        response.setContentType(MediaType.APPLICATION_JSON_VALUE);
        // 字符编码 UTF-8
        response.setCharacterEncoding(StandardCharsets.UTF_8.displayName());
        try (PrintWriter writer = response.getWriter()) {
            ObjectMapper o = new ObjectMapper();
            // 写入一个友好的数据返回
            writer.append(o.writeValueAsString(
                    new CodedMessage<>(HttpStatus.UNAUTHORIZED.value(), authException.getMessage())
            ));
            writer.flush();
        }
    }
}
```

## 配置类

```java
package tech.tystnad.domain.auth.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.AuthenticationProvider;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import tech.tystnad.domain.auth.filter.JwtAuthenticationFilter;
import tech.tystnad.domain.auth.provider.InnerAccessDeniedHandler;
import tech.tystnad.domain.auth.provider.InnerAuthenticationEntryPoint;

/**
 * Spring Security 配置
 * 1. 禁用某些不需要的操作
 * 2. 增加 JWT 的处理过滤器
 * 3. 增加鉴权异常处理操作
 */
@Configuration
@EnableWebSecurity
public class SecurityConfiguration {

    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final AuthenticationProvider authenticationProvider;
    private final InnerAccessDeniedHandler accessDeniedHandler;
    private final InnerAuthenticationEntryPoint authenticationEntryPoint;

    public SecurityConfiguration(JwtAuthenticationFilter jwtAuthenticationFilter, AuthenticationProvider authenticationProvider,
                                 InnerAccessDeniedHandler accessDeniedHandler, InnerAuthenticationEntryPoint authenticationEntryPoint) {
        this.jwtAuthenticationFilter = jwtAuthenticationFilter;
        this.authenticationProvider = authenticationProvider;
        this.accessDeniedHandler = accessDeniedHandler;
        this.authenticationEntryPoint = authenticationEntryPoint;
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {
        return configuration.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.csrf().disable(); // 禁用跨站请求伪造
        http.headers().frameOptions().disable(); // 允许内联表单
        http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS); // 由于通过口令鉴权，所以可改为无服务状态
        http.authorizeHttpRequests()
                // 登录和注册接口开放
                .requestMatchers("/test", "/test/**").permitAll()
                .requestMatchers("/login").permitAll()
                .requestMatchers(HttpMethod.POST, "/accounts").permitAll()
                .anyRequest().authenticated()
                .and().authenticationProvider(authenticationProvider)
                // 追加 JWT 校验的过滤器到 spring security 的过滤器之前, 先处理并注入有效的鉴权账户
                .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);
        http.exceptionHandling()
                // 增加权限无效(401)和拒绝访问(403)的鉴权错误处理程序
                .accessDeniedHandler(accessDeniedHandler)
                .authenticationEntryPoint(authenticationEntryPoint);
        return http.build();
    }
}
```

Last Modified 2023-03-14
