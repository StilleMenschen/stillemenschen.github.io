# 枚举的序列化和持久化

使用`Java 17`和`Spring Boot 3.0`

## 依赖

使用最新的`maven`，加入 Web、JPA、Validation 和`PostgreSQL`数据库

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>enumerated</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>enumerated</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
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
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

配置文件

```properties
spring.jpa.open-in-view=false
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.datasource.url=jdbc:postgresql://${SQL_DB_HOST:localhost}:5432/enumerated
spring.datasource.username=postgres
spring.datasource.password=654321
logging.level.root=warn
logging.level.com.example.enumerated=debug
logging.level.org.hibernate.SQL=debug
```

```sql
CREATE DATABASE enumerated;
```

## 基础数据

```java
package com.example.enumerated;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonValue;

import java.util.stream.Stream;

public enum OrderType {
    DRINKS(1, "0001", "饮料"),
    SNACK(2, "0002", "快餐"),
    FRESH(3, "0003", "生鲜");
    private final int code;
    private final String value;
    private final String description;

    OrderType(int code, String value, String description) {
        this.code = code;
        this.value = value;
        this.description = description;
    }


    /**
     * 加上{@link JsonValue}注解后，Jackson序列化时取这个方法的返回值
     */
    @JsonValue
    public int code() {
        return code;
    }

    public String value() {
        return value;
    }

    public String description() {
        return description;
    }

    /**
     * 加上{@link JsonCreator}后反序列化会参考此方法来创建枚举实例
     *
     * @param code 参考值
     * @return 枚举数据
     */
    @JsonCreator
    public static OrderType fromCode(int code) {
        return Stream.of(values()).filter(it -> it.code == code)
                .findFirst().orElse(SNACK);
    }
}
```

```java
package com.example.enumerated;

import jakarta.persistence.AttributeConverter;
import jakarta.persistence.Converter;

/**
 * JPA 持久化时参考此转换器来处理枚举值
 */
@Converter(autoApply = true)
public class OrderTypeConverter implements AttributeConverter<OrderType, Integer> {
    /**
     * 转换为存入数据库的数据
     */
    @Override
    public Integer convertToDatabaseColumn(OrderType attribute) {
        if (null == attribute) return null;
        return attribute.code();
    }

    /**
     * 转换为读取出来后的实际枚举
     */
    @Override
    public OrderType convertToEntityAttribute(Integer dbData) {
        if (null == dbData) return null;
        return OrderType.fromCode(dbData);
    }
}
```

```java
package com.example.enumerated;

import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.databind.annotation.JsonDeserialize;
import com.fasterxml.jackson.databind.annotation.JsonSerialize;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;
import jakarta.persistence.*;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import lombok.*;

import java.math.BigDecimal;
import java.time.LocalDateTime;

@Getter
@Setter
@ToString
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    @NotNull
    @Min(1)
    private BigDecimal quantity;
    @NotNull
    @Min(1)
    private BigDecimal price;
    @NotNull
    @Min(1)
    private BigDecimal amount;
    @NotBlank
    private String description;
    /**
     * 需要告诉 JPA 使用什么转换器来处理枚举
     */
    @Convert(converter = OrderTypeConverter.class)
    private OrderType orderType;
    @Column(updatable = false) // 不会添加到更新操作的 SQL 语句中
    @JsonSerialize(using = LocalDateTimeSerializer.class)
    @JsonDeserialize(using = LocalDateTimeDeserializer.class)
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createAt;
}
```

```java
package com.example.enumerated;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
}
```

## 请求处理

```java
package com.example.enumerated;

import jakarta.validation.Valid;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDateTime;
import java.util.List;
import java.util.NoSuchElementException;

@RestController
@RequestMapping("/order")
public class OrderController {

    private final OrderRepository repository;

    public OrderController(OrderRepository repository) {
        this.repository = repository;
    }

    @GetMapping
    public List<Order> getAll() {
        return repository.findAll();
    }

    @GetMapping("/{id}")
    public Order getById(@PathVariable("id") Long id) {
        return repository.findById(id).orElseThrow();
    }

    @PutMapping
    public Order add(@RequestBody @Valid Order order) {
        if (null == order) {
            throw new NullPointerException();
        }
        order.setCreateAt(LocalDateTime.now());
        return repository.save(order);
    }

    @PostMapping
    public Order modify(@RequestBody @Valid Order order) {
        if (null == order || null == order.getId()) {
            throw new NullPointerException();
        }
        if (repository.findById(order.getId()).isEmpty()) {
            throw new NoSuchElementException();
        }
        return repository.save(order);
    }
}
```

```java
package com.example.enumerated;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.math.BigDecimal;
import java.time.LocalDateTime;

@SpringBootApplication
public class EnumeratedApplication {

    private final OrderRepository repository;

    public EnumeratedApplication(OrderRepository repository) {
        this.repository = repository;
    }

    public static void main(String[] args) {
        SpringApplication.run(EnumeratedApplication.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner() {
        return args -> {
            repository.save(Order.builder()
                    .quantity(BigDecimal.valueOf(1))
                    .price(BigDecimal.valueOf(10))
                    .amount(BigDecimal.valueOf(20))
                    .description("又是什么")
                    .orderType(OrderType.DRINKS)
                    .createAt(LocalDateTime.now())
                    .build());
            repository.save(Order.builder()
                    .quantity(BigDecimal.valueOf(3))
                    .price(BigDecimal.valueOf(30))
                    .amount(BigDecimal.valueOf(90))
                    .description("还是什么")
                    .orderType(OrderType.SNACK)
                    .createAt(LocalDateTime.now())
                    .build());
            repository.save(Order.builder()
                    .quantity(BigDecimal.valueOf(9))
                    .price(BigDecimal.valueOf(20))
                    .amount(BigDecimal.valueOf(180))
                    .description("再是什么")
                    .orderType(OrderType.FRESH)
                    .createAt(LocalDateTime.now())
                    .build());
        };
    }
}
```

## 示例

### 新增订单

```http
PUT http://localhost:8080/order HTTP/1.1
Content-Type: application/json

{
  "quantity": 2.00,
  "price": 20.00,
  "amount": 40.00,
  "description": "不能是什么",
  "orderType": 1
}
```

```json
{
  "id": 4,
  "quantity": 2.00,
  "price": 20.00,
  "amount": 40.00,
  "description": "不能是什么",
  "orderType": 1,
  "createAt": "2023-04-01 20:57:29"
}
```

### 修改订单

```http
POST http://localhost:8080/order HTTP/1.1
Content-Type: application/json

{
  "id": 4,
  "quantity": 12.00,
  "price": 120.00,
  "amount": 140.00,
  "description": "不能是什么",
  "orderType": 3
}
```

```json
{
  "id": 4,
  "quantity": 12.00,
  "price": 120.00,
  "amount": 140.00,
  "description": "不能是什么",
  "orderType": 3,
  "createAt": null
}
```

### 查询一个订单

```http
GET http://localhost:8080/order/4 HTTP/1.1
```

```json
{
  "id": 4,
  "quantity": 12.00,
  "price": 120.00,
  "amount": 140.00,
  "description": "不能是什么",
  "orderType": 3,
  "createAt": "2023-04-01 20:57:29"
}
```

### 查询所有订单

```http
GET http://localhost:8080/order HTTP/1.1
```

```json
[
    {
        "id": 1,
        "quantity": 1.00,
        "price": 10.00,
        "amount": 20.00,
        "description": "又是什么",
        "orderType": 1,
        "createAt": "2023-04-01 21:02:01"
    },
    {
        "id": 2,
        "quantity": 3.00,
        "price": 30.00,
        "amount": 90.00,
        "description": "还是什么",
        "orderType": 2,
        "createAt": "2023-04-01 21:02:01"
    },
    {
        "id": 3,
        "quantity": 9.00,
        "price": 20.00,
        "amount": 180.00,
        "description": "再是什么",
        "orderType": 3,
        "createAt": "2023-04-01 21:02:01"
    },
    {
        "id": 4,
        "quantity": 12.00,
        "price": 120.00,
        "amount": 140.00,
        "description": "不能是什么",
        "orderType": 3,
        "createAt": "2023-04-01 21:02:34"
    }
]
```

## 数据库

```sql
select id,quantity,price,amount,description,order_type,create_at from orders;
```

```raw
 id | quantity | price  | amount | description | order_type |         create_at
----+----------+--------+--------+-------------+------------+----------------------------
  1 |     1.00 |  10.00 |  20.00 | 又是什么    |          1 | 2023-04-01 21:02:01.304611
  2 |     3.00 |  30.00 |  90.00 | 还是什么    |          2 | 2023-04-01 21:02:01.352352
  3 |     9.00 |  20.00 | 180.00 | 再是什么    |          3 | 2023-04-01 21:02:01.356028
  4 |    12.00 | 120.00 | 140.00 | 不能是什么  |          3 | 2023-04-01 21:02:34.697775
(4 rows)
```

Last Modified 2023-04-01
