# String Trimmer

## 去除字符串前后空格

```java
package tech.tystnad.infrastructure.format;

import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.databind.DeserializationContext;
import com.fasterxml.jackson.databind.deser.std.StdScalarDeserializer;
import jakarta.servlet.Servlet;
import org.springframework.beans.propertyeditors.StringTrimmerEditor;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
import org.springframework.boot.autoconfigure.jackson.Jackson2ObjectMapperBuilderCustomizer;
import org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.servlet.DispatcherServlet;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.io.IOException;

/**
 * 全局去除传入数据中 {@link String} 类型数据的前后空格
 */
@Configuration
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)
@ConditionalOnClass({Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class})
@AutoConfigureAfter(WebMvcAutoConfiguration.class)
public class StringTrimmerConfiguration {

    /**
     * 在每个声明了 {@link org.springframework.stereotype.Controller} 中执行固定的操作
     */
    @ControllerAdvice
    public static class ControllerStringParamTrimConfig {

        /**
         * url和 form 表单中的参数 trim
         */
        @InitBinder
        public void initBinder(WebDataBinder binder) {
            // 构造方法中 boolean 参数含义为如果是空白字符串,是否转换为null
            // 即如果为true,那么 " " 会被转换为 null,否者为 ""
            StringTrimmerEditor stringTrimmerEditor = new StringTrimmerEditor("\n\r\f", true);
            binder.registerCustomEditor(String.class, stringTrimmerEditor);
        }
    }

    /**
     * Request Body 中 JSON 或 XML 对象参数 trim
     */
    @Bean
    public Jackson2ObjectMapperBuilderCustomizer jackson2ObjectMapperBuilderCustomizer() {
        return jacksonObjectMapperBuilder -> jacksonObjectMapperBuilder
                .deserializerByType(String.class, new StdScalarDeserializer<String>(String.class) {
                    @Override
                    public String deserialize(JsonParser jsonParser, DeserializationContext ctx)
                            throws IOException {
                        String value = jsonParser.getValueAsString();
                        if (StringUtils.hasText(value)) {
                            // 非空值且不是只有空格的情况下处理返回
                            return value.trim();
                        }
                        return null;
                    }
                });
    }
}
```

Last Modified 2023-03-17
