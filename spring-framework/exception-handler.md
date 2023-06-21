# Exception Handler

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

## 声明共享的控制器类

声明了注解`RestControllerAdvice`或者`ControllerAdvice`的类，会在所有控制器中共享，拓展`ResponseEntityExceptionHandler`可以将一些异常处理交给`Spring`

```java
package tech.tystnad.infrastructure.handler;

import jakarta.validation.ConstraintViolation;
import jakarta.validation.ConstraintViolationException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.HttpStatusCode;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.ObjectError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;
import tech.tystnad.infrastructure.jaxrs.CodedMessage;

import java.util.Objects;
import java.util.Optional;
import java.util.Set;

/**
 * 内部异常处理，增加特殊控制器注解 {@link RestControllerAdvice}， 将异常处理的操作共享给所有的控制器
 * 针对不同的异常返回友好的数据，参考 {@link ResponseEntityExceptionHandler} 里面的 <b>handleExceptionInternal</b> 方法
 */
@RestControllerAdvice
public class InnerExceptionHandler extends ResponseEntityExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(InnerExceptionHandler.class);

    @Override
    @SuppressWarnings("NullableProblems")
    protected ResponseEntity<Object> handleExceptionInternal(Exception ex, Object body, HttpHeaders headers,
                                                             HttpStatusCode statusCode, WebRequest request) {
        // 处理参数校验的错误，将校验的错误提示返回
        if (ex instanceof MethodArgumentNotValidException validException) {
            // 只取出第一个错误信息返回
            ObjectError objectError = validException.getAllErrors().get(0);
            body = new CodedMessage<>(statusCode.value(), objectError.getDefaultMessage(), null);
        }
        // 处理方法参数自定义注解校验的错误
        else if (ex instanceof ConstraintViolationException violationException) {
            // 只取出第一个错误信息返回
            Set<ConstraintViolation<?>> violationSet = violationException.getConstraintViolations();
            body = new CodedMessage<>(statusCode.value(), violationSet.iterator().next().getMessage(), null);
        } else {
            // 所有错误
            body = new CodedMessage<>(statusCode.value(), ex.getMessage(), null);
        }
        // 输出到日志中
        log.error(ex.getMessage(), ex);
        return super.handleExceptionInternal(ex, body, headers, statusCode, request);
    }

    /**
     * 通过注解来声明需要处理的参数并传给主要的方法处理
     *
     * @param ex 对应的{@link ConstraintViolationException}异常
     */
    @ExceptionHandler(ConstraintViolationException.class)
    public final ResponseEntity<Object> handleConstraintViolationException(ConstraintViolationException ex, WebRequest request) {
        // 这里返回的状态码为入参错误 HTTP 400
        return handleExceptionInternal(ex, null, HttpHeaders.EMPTY, HttpStatus.BAD_REQUEST, request);
    }

    /**
     * 处理所有错误，考虑获取原始错误的问题
     */
    @ExceptionHandler(Exception.class)
    public final ResponseEntity<Object> handleUnknownException(Exception ex, WebRequest request) {
        Throwable originalCause = ex;
        int depth = 1;
        // 循环获取原始错误，最大操作42次
        while (null != originalCause.getCause() && depth <= 42) {
            depth++;
            originalCause = originalCause.getCause();
        }
        return handleExceptionInternal((Exception) originalCause, null, HttpHeaders.EMPTY, HttpStatus.INTERNAL_SERVER_ERROR, request);
    }
}
```

Last Modified 2023-06-21
