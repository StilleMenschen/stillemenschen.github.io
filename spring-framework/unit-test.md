# 单元测试

## 依赖和配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.1.0</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>mock-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mock-test</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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

```properties
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
spring.data.mongodb.database=test
spring.data.mongodb.username=root
spring.data.mongodb.password=example
spring.data.mongodb.uri=mongodb://root:example@localhost:27017/test?authSource=admin&authMechanism=SCRAM-SHA-1
```

## 关键类

AccountRepository.java

```java
package com.example.mocktest;

import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface AccountRepository extends MongoRepository<Account, String> {

    boolean existsByEmail(String email);
}
```

AccountService.java

```java
package com.example.mocktest;

import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class AccountService {
    private final AccountRepository accountRepository;

    public AccountService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }

    public List<Account> getAllAccounts() {
        return accountRepository.findAll();
    }

    public void addAccount(Account account) {
        String email = account.getEmail();
        if (accountRepository.existsByEmail(email)) {
            throw new IllegalArgumentException("Email " + email + " already exists");
        }
        accountRepository.save(account);
    }

    public void deleteAccount(String id) {
        if (!accountRepository.existsById(id)) {
            throw new IllegalArgumentException("account does not exists");
        }
        accountRepository.deleteById(id);
    }
}
```

## 测试类

AccountRepositoryTest.java

```java
package com.example.mocktest;

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.time.LocalDateTime;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

@SpringBootTest
class AccountRepositoryTest {

    private final AccountRepository accountRepository;

    public AccountRepositoryTest(@Autowired AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }

    @AfterEach
    void tearDown() {
        accountRepository.deleteAll();
    }

    @Test
    void itShouldCheckWhenEmailExists() {
        // given
        String email = "jack@gmail.com";
        Account account = new Account("jack", email, LocalDateTime.now());
        accountRepository.save(account);

        // when
        boolean exists = accountRepository.existsByEmail(email);

        // then
        assertThat(exists).isTrue();
    }

    @Test
    void itShouldCheckWhenEmailDoesNotExists() {
        // given
        String email = "jack@gmail.com";

        // when
        boolean exists = accountRepository.existsByEmail(email);

        // then
        assertThat(exists).isFalse();
    }
}
```

AccountServiceTest.java

```java
package com.example.mocktest;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Disabled;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.time.LocalDateTime;
import java.util.List;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.assertj.core.api.AssertionsForClassTypes.assertThatThrownBy;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.never;
import static org.mockito.Mockito.verify;

@ExtendWith(MockitoExtension.class)
class AccountServiceTest {

    @Mock // 模拟类
    private AccountRepository accountRepository;
    private AccountService accountService;

    @BeforeEach
    void setUp() {
        accountService = new AccountService(accountRepository);
    }

    @Test
    void CanGetAllAccounts() {
        // when
        List<Account> allAccounts = accountService.getAllAccounts();

        // then
        assertThat(allAccounts).isNotNull();
        verify(accountRepository).findAll(); // 验证方法是否被调用
    }

    @Test
    void CanAddAccount() {
        // given
        String email = "jack@gmail.com";
        Account account = new Account("jack", email, LocalDateTime.now());

        // when
        accountService.addAccount(account);

        // then
        ArgumentCaptor<Account> accountArgumentCaptor = ArgumentCaptor.forClass(Account.class);

        verify(accountRepository).save(accountArgumentCaptor.capture()); // 捕捉方法调用传入的参数

        Account capturedAccount = accountArgumentCaptor.getValue();

        assertThat(capturedAccount).isEqualTo(account);
    }

    @Test
    void willThrowWhenEmailExists() {
        // given
        String email = "jack@gmail.com";
        Account account = new Account("jack", email, LocalDateTime.now());

        // when
        given(accountRepository.existsByEmail(anyString())).willReturn(true); // 修改方法的返回值

        // then
        assertThatThrownBy(() -> accountService.addAccount(account))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessageContaining("Email " + email + " already exists");

        verify(accountRepository, never()).save(any()); // 验证方法未被调用
    }

    @Test
    @Disabled // 被禁用的测试
    void deleteAccount() {
    }
}
```

> 可以通过 IDE 提供的测试代码覆盖率工具来查看测试类覆盖的代码，如`IntelliJ IDEA`的`Run xxx with Coverage`

Last Modified 2023-06-04
