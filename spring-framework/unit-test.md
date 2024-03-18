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
        <version>3.2.3</version>
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
                <version>3.2.3</version>
            </plugin>
        </plugins>
    </build>

</project>
```

本地安装了 MongoDB，配置用户名密码 root:example

```properties
spring.data.mongodb.uri=mongodb://root:example@localhost:27017/test?authSource=admin&authMechanism=SCRAM-SHA-1

logging.level.root=warn
logging.level.com.example.mocktest=debug
```

## Account 测试

Account.java

```java
package com.example.mocktest;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

import java.time.LocalDateTime;

@Document(collection = "account")
public class Account {

    @Id
    private String id;
    private String name;
    private String email;
    private LocalDateTime createdAt;

    public Account() {
    }

    public Account(String id, String name, String email, LocalDateTime createdAt) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.createdAt = createdAt;
    }

    public Account(String name, String email, LocalDateTime createdAt) {
        this.name = name;
        this.email = email;
        this.createdAt = createdAt;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }

    public void setCreatedAt(LocalDateTime createdAt) {
        this.createdAt = createdAt;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", createdAt=" + createdAt +
                '}';
    }
}
```

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

## Calculator 测试

Calculator.java

```java
package com.example.mocktest;

public class Calculator {
    private final CalculatorService calculatorService;

    public Calculator() {
        this.calculatorService = null;
    }

    public Calculator(CalculatorService calculatorService) {
        this.calculatorService = calculatorService;
    }

    public int performAddition(int a, int b) {
        return calculatorService.add(a, b);
    }

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }
}
```

CalculatorService.java

```java
package com.example.mocktest;

public interface CalculatorService {
    int add(int a, int b);
}
```

CalculatorTest.java

```java
package com.example.mocktest;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Spy;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
public class CalculatorTest {

    @Spy
    private Calculator calculatorSpy;

    @Test
    public void testPerformAdditionWithArgumentMatchers() {
        // 创建 CalculatorService 的模拟对象
        CalculatorService calculatorServiceMock = mock(CalculatorService.class);

        // 定义模拟对象的行为：当调用 add 方法时，使用参数匹配器来匹配参数
        when(calculatorServiceMock.add(anyInt(), anyInt())).thenReturn(10);

        // 实例化被测试的 Calculator 对象，并传入模拟的 CalculatorService
        Calculator calculator = new Calculator(calculatorServiceMock);

        // 调用被测试的方法，并传入任意整数作为参数
        int result = calculator.performAddition(5, 3);

        // 验证返回值是否正确
        assertEquals(10, result);

        // 验证是否调用了 add 方法，并且参数匹配正确
        verify(calculatorServiceMock).add(anyInt(), anyInt());
    }

    @Test
    public void testSpyObject() {
        // 创建实际的 Calculator 对象
        Calculator calculator = new Calculator();

        // 使用Spy对象来部分模拟 Calculator 对象
        Calculator calculatorSpy = spy(calculator);

        // 定义模拟对象的行为：部分模拟 add 方法，但保留实际的 subtract 方法
        when(calculatorSpy.add(2, 3)).thenReturn(10);

        // 调用实际的 subtract 方法
        int result = calculatorSpy.subtract(5, 2);

        // 验证实际的 subtract 方法是否按预期执行
        assertEquals(3, result);

        // 验证模拟的 add 方法是否按预期执行
        assertEquals(10, calculatorSpy.add(2, 3));

        // 验证实际的 add 方法是否按预期执行
        assertEquals(7, calculatorSpy.add(4, 3));
    }

    @Test
    public void testSubtractWithSpy() {
        // 使用真实的add方法
        int result1 = calculatorSpy.add(5, 3);
        assertEquals(8, result1);

        // 部分模拟subtract方法
        doReturn(2).when(calculatorSpy).subtract(5, 3);

        // 使用部分模拟的subtract方法
        int result2 = calculatorSpy.subtract(5, 3);
        assertEquals(2, result2);

        // 使用真实的subtract方法
        int result3 = calculatorSpy.subtract(10, 3);
        assertEquals(7, result3);
    }
}
```

## Payment 测试

PaymentService.java

```java
package com.example.mocktest;

public interface PaymentService {
    void processPayment(double amount);
    void refundPayment(double amount);
}
```

PaymentProcessor.java

```java
package com.example.mocktest;

public class PaymentProcessor {
    private final PaymentService paymentService;

    public PaymentProcessor(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public void makePayment(double amount) {
        paymentService.processPayment(amount);
    }

    public void refund(double amount) {
        paymentService.refundPayment(amount);
    }
}
```

PaymentProcessorTest.java

```java
package com.example.mocktest;

import static org.mockito.Mockito.*;
import org.junit.jupiter.api.Test;
import org.mockito.InOrder;

public class PaymentProcessorTest {

    @Test
    public void testMakePayment() {
        // 创建 PaymentService 的模拟对象
        PaymentService paymentServiceMock = mock(PaymentService.class);

        // 实例化被测试的 PaymentProcessor 对象，并传入模拟的 PaymentService
        PaymentProcessor paymentProcessor = new PaymentProcessor(paymentServiceMock);

        // 调用被测试的方法
        paymentProcessor.makePayment(100.0);

        // 验证是否正确调用了 processPayment 方法，并且传入了 100.0 作为参数
        verify(paymentServiceMock).processPayment(100.0);
    }

    @Test
    public void testRefund() {
        // 创建 PaymentService 的模拟对象
        PaymentService paymentServiceMock = mock(PaymentService.class);

        // 实例化被测试的 PaymentProcessor 对象，并传入模拟的 PaymentService
        PaymentProcessor paymentProcessor = new PaymentProcessor(paymentServiceMock);

        // 调用被测试的方法
        paymentProcessor.refund(50.0);

        // 验证是否正确调用了 refundPayment 方法，并且传入了 50.0 作为参数
        verify(paymentServiceMock).refundPayment(50.0);
    }

    @Test
    public void testMakePaymentMultipleTimes() {
        // 创建 PaymentService 的模拟对象
        PaymentService paymentServiceMock = mock(PaymentService.class);

        // 实例化被测试的 PaymentProcessor 对象，并传入模拟的 PaymentService
        PaymentProcessor paymentProcessor = new PaymentProcessor(paymentServiceMock);

        // 调用被测试的方法两次
        paymentProcessor.makePayment(100.0);
        paymentProcessor.makePayment(200.0);

        // 验证是否正确调用了 processPayment 方法，并且分别传入了 100.0 和 200.0 作为参数，而且顺序是正确的
        InOrder inOrder = inOrder(paymentServiceMock);
        inOrder.verify(paymentServiceMock).processPayment(100.0);
        inOrder.verify(paymentServiceMock).processPayment(200.0);
    }
}
```

## User 测试

User.java

```java
package com.example.mocktest;

public class User {
    private Integer id;
    private String username;
    private String email;

    public User(Integer id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    public User(String username, String email) {
        this.id = -1;
        this.username = username;
        this.email = email;
    }

    public User(int id, String username) {
        this.id = id;
        this.username = username;
        this.email = null;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

UserService.java

```java
package com.example.mocktest;

public interface UserService {
    User getUserById(int id);
    void updateUser(User user);
    void createUser(String username);
    void updateUser(String username);
    void deleteUser(String username);

    void processRequest(String request);
}
```

UserServiceImpl.java

```java
package com.example.mocktest;

public class UserServiceImpl implements UserService {
    @Override
    public User getUserById(int id) {
        return null;
    }

    @Override
    public void updateUser(User user) {

    }

    @Override
    public void createUser(String username) {

    }

    @Override
    public void updateUser(String username) {

    }

    @Override
    public void deleteUser(String username) {

    }

    @Override
    public void processRequest(String request) {

    }
}
```

UserManager.java

```java
package com.example.mocktest;

public class UserManager {
    private final UserService userService;

    public UserManager(UserService userService) {
        this.userService = userService;
    }

    public String getUsername(int userId) {
        User user = userService.getUserById(userId);
        if (user != null) {
            return user.getUsername();
        } else {
            return "User not found";
        }
    }

    public void updateUserEmail(int userId, String newEmail) {
        User user = userService.getUserById(userId);
        if (user != null) {
            user.setEmail(newEmail);
            userService.updateUser(user);
        }
    }

    public void updateUserAndEmail(User user, String newEmail) {
        try {
            userService.updateUser(user);
            user.setEmail(newEmail);
        } catch (IllegalArgumentException e) {
            // 处理异常
            System.out.println("IllegalArgumentException caught: " + e.getMessage());
        }
    }

    public void manageUser(String username) {
        userService.createUser(username);
        userService.updateUser(username);
        userService.deleteUser(username);
    }

    public void handleRequest(String request) {
        userService.processRequest(request);
    }
}
```

UserServiceTest.java

```java
package com.example.mocktest;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.*;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.verify;

public class UserServiceTest {

    @Mock
    private UserService userServiceMock;

    @Captor
    private ArgumentCaptor<String> requestCaptor;

    @InjectMocks
    private UserManager userManager;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testProcessRequest() {
        // 调用被测试的方法
        userManager.handleRequest("Some request");

        // 验证模拟方法是否被调用，并捕获参数
        verify(userServiceMock).processRequest(requestCaptor.capture());

        // 获取捕获的参数值
        String capturedRequest = requestCaptor.getValue();

        // 进行进一步的断言
        assertEquals("Some request", capturedRequest);
    }
}
```

UserManagerTest.java

```java
package com.example.mocktest;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Timeout;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InOrder;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.concurrent.TimeUnit;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotEquals;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class UserManagerTest {

    @Mock
    private UserService userServiceMock;

    @InjectMocks
    private UserManager userManager;

    @Test
    public void testGetUsername() {
        // 创建 UserService 的模拟对象
        UserService userServiceMock = mock(UserService.class);

        // 定义模拟对象的行为：当调用 getUserById 方法时，返回一个模拟的用户对象
        when(userServiceMock.getUserById(1)).thenReturn(new User(1, "John"));

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceMock);

        // 执行被测试的方法
        String username = userManager.getUsername(1);

        // 验证结果
        assertEquals("John", username);
    }

    @Test
    public void testUpdateUserEmail() {
        // 创建 UserService 的模拟对象
        UserService userServiceMock = mock(UserService.class);

        when(userServiceMock.getUserById(1)).thenReturn(new User(1, "John"));

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceMock);

        // 调用被测试的方法
        userManager.updateUserEmail(1, "john@example.com");

        // 验证是否正确调用了 userService 的 updateUser 方法
        verify(userServiceMock).updateUser(any(User.class));
    }

    @Test
    public void testUpdateUserEmail_ExceptionHandling() {
        // 创建 UserService 的模拟对象
        UserService userServiceMock = mock(UserService.class);

        // 定义模拟对象的行为：当调用 updateUser 方法时抛出 IllegalArgumentException 异常
        doThrow(new IllegalArgumentException("Invalid user")).when(userServiceMock).updateUser(any(User.class));

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceMock);

        // 调用被测试的方法，传入一个用户对象和新的电子邮件地址
        User user = new User("John", "john@example.com");
        userManager.updateUserAndEmail(user, "newemail@example.com");

        // 验证被测试的方法是否正确处理了异常情况
        assertNotEquals("newemail@example.com", user.getEmail());
    }

    @Test
    public void testManageUserInOrder() {
        // 创建 UserService 的模拟对象
        UserService userServiceMock = mock(UserService.class);

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceMock);

        // 调用被测试的方法
        userManager.manageUser("John");

        // 验证方法调用的顺序是否符合预期
        InOrder inOrder = inOrder(userServiceMock);
        inOrder.verify(userServiceMock).createUser("John");
        inOrder.verify(userServiceMock).updateUser("John");
        inOrder.verify(userServiceMock).deleteUser("John");
    }

    @Test
    public void testHandleRequest() {
        // 创建 UserService 的模拟对象
        UserService userServiceMock = mock(UserService.class);

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceMock);

        // 调用被测试的方法
        userManager.handleRequest("Some request");

        // 验证方法调用在100毫秒内是否完成
        verify(userServiceMock, timeout(100)).processRequest("Some request");
    }

    @Test
    @Timeout(value = 200, unit = TimeUnit.MILLISECONDS) // 执行超时 200 毫秒
    public void testHandleRequest_ExecutionTimeExceedsTimeout() {
        // 创建 UserService 的实际对象
        UserService userService = new UserServiceImpl(); // 假设实现了UserService接口并具有processRequest方法

        // 创建 UserService 的 Spy 对象
        UserService userServiceSpy = spy(userService);

        // 实例化被测试的 UserManager 对象，并传入模拟的 UserService
        UserManager userManager = new UserManager(userServiceSpy);

        // 在模拟的 UserService 的 processRequest 方法中添加延迟
        doAnswer(invocation -> {
            try {
                TimeUnit.MILLISECONDS.sleep(100L);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            return null;
        }).when(userServiceSpy).processRequest(anyString());

        // 调用被测试的方法
        userManager.handleRequest("Some request");

        // 验证方法调用在100毫秒内是否未完成
        verify(userServiceSpy, timeout(100)).processRequest(anyString());
    }
}
```

Last Modified 2024-03-18
