# Hibernate Validator

## 配置类

```java
package tech.tystnad.infrastructure.validation;

import jakarta.validation.Validation;
import jakarta.validation.Validator;
import jakarta.validation.ValidatorFactory;
import org.hibernate.validator.HibernateValidator;
import org.springframework.beans.factory.config.AutowireCapableBeanFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.validation.beanvalidation.SpringConstraintValidatorFactory;

/**
 * 校验器的配置
 */
@Configuration
public class ValidatorConfiguration {

    @Bean
    public Validator validator(AutowireCapableBeanFactory springFactory) {
        // 针对 Hibernate 的校验配置
        try (ValidatorFactory factory = Validation.byProvider(HibernateValidator.class)
                .configure()
                // 快速失败，遇到第一个参数校验错误则直接结束校验并返回错误
                .failFast(true)
                // 解决 SpringBoot 依赖注入问题
                .constraintValidatorFactory(new SpringConstraintValidatorFactory(springFactory))
                .buildValidatorFactory()) {
            return factory.getValidator();
        }
    }
}
```

## 实体类

```java
package tech.tystnad.domain.account;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.persistence.*;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Pattern;
import lombok.*;
import org.hibernate.Hibernate;
import tech.tystnad.domain.BaseEntity;

import java.util.Objects;

@Getter
@Setter
@ToString
@Entity
@Builder
@Table(name = "account")
@AllArgsConstructor
@NoArgsConstructor
public class Account extends BaseEntity {
    @NotBlank(message = "用户账号不允许为空")
    private String username;
    // 密码字段不参与序列化（但反序列化是参与的）、不参与更新（但插入是参与的）
    // 这意味着密码字段不会在获取对象（很多操作都会关联用户对象）的时候泄漏出去；
    // 也意味着此时“修改密码”一类的功能无法以用户对象资源的接口来处理（因为更新对象时密码不会被更新），需要单独提供接口去完成
    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @Column(updatable = false)
    @NotBlank(message = "密码不能为空")
    private String password;
    @Email(message = "邮箱格式不正确")
    @NotBlank(message = "邮箱不允许为空")
    private String email;
    @Pattern(regexp = "1\\d{10}", message = "手机号格式不正确")
    @NotBlank(message = "手机号不允许为空")
    private String telephone;
    @NotBlank(message = "用户姓名不允许为空")
    private String name;
    @Enumerated(EnumType.STRING)
    private Role role;
    // 通过其它方式来启用和禁用
    @Column(updatable = false)
    private Boolean enabled;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || Hibernate.getClass(this) != Hibernate.getClass(o)) return false;
        Account account = (Account) o;
        return getId() != null && Objects.equals(getId(), account.getId());
    }

    @Override
    public int hashCode() {
        return getClass().hashCode();
    }
}
```

```java
package tech.tystnad.domain.auth;

import org.springframework.beans.BeanUtils;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import tech.tystnad.domain.account.Account;
import tech.tystnad.domain.account.Role;

import java.util.Collection;
import java.util.HashSet;


public class AuthenticAccount extends Account implements UserDetails {


    /**
     * 该用户拥有的授权，譬如读取权限、修改权限、增加权限等等
     */
    private final Collection<GrantedAuthority> authorities = new HashSet<>();

    public AuthenticAccount(Account origin) {
        this();
        BeanUtils.copyProperties(origin, this);
        if (getId() == 1) {
            // 由于没有做用户管理功能，默认给系统中第一个用户赋予管理员角色
            authorities.add(new SimpleGrantedAuthority(Role.ROLE_ADMIN.name()));
        }
    }

    public AuthenticAccount() {
        super();
        authorities.add(new SimpleGrantedAuthority(Role.ROLE_USER.name()));
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public boolean isAccountNonExpired() {
        return getEnabled();
    }

    @Override
    public boolean isAccountNonLocked() {
        return getEnabled();
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return getEnabled();
    }

    @Override
    public boolean isEnabled() {
        return getEnabled();
    }
}
```

```java
package tech.tystnad.domain.account;

public enum Role {
    ROLE_USER,
    ROLE_ADMIN
}
```

## 持久化

```java
package tech.tystnad.domain.account;

import org.springframework.cache.annotation.CacheConfig;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.cache.annotation.Caching;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

@Repository
@CacheConfig(cacheNames = "repository.account")
@SuppressWarnings("NullableProblems")
public interface AccountRepository extends CrudRepository<Account, Long> {

    @Cacheable(key = "#username", unless = "#result == null")
    Account findByUsername(String username);

    /**
     * 判断存在性，用户名存在即为存在
     */
    boolean existsByUsername(String username);

    boolean existsByEmail(String email);

    boolean existsByTelephone(String telephone);

    /**
     * 判断唯一性，用户名、邮箱、电话不允许任何一个重复
     */
    boolean existsByUsernameOrEmailOrTelephone(String username, String email, String telephone);

    /**
     * 判断唯一性，用户名、邮箱、电话不允许任何一个重复
     */
    List<Account> findByUsernameOrEmailOrTelephone(String username, String email, String telephone);

    // 覆盖以下父类中需要处理缓存失效的方法
    // 父类取不到CacheConfig的配置信息，所以不能抽象成一个通用的父类接口中完成
    @Caching(evict = {
            @CacheEvict(key = "#entity.id"),
            @CacheEvict(key = "#entity.username")
    })
    <S extends Account> S save(S entity);

    @CacheEvict
    <S extends Account> Iterable<S> saveAll(Iterable<S> entities);

    @Cacheable(key = "#id")
    Optional<Account> findById(Long id);

    @Cacheable(key = "#id")
    boolean existsById(Long id);

    @CacheEvict(key = "#entity.id")
    void delete(Account entity);

    @CacheEvict(allEntries = true)
    void deleteAll(Iterable<? extends Account> entities);

    @CacheEvict(allEntries = true)
    void deleteAll();

}
```

## 自定义注解

```java
package tech.tystnad.domain.account.validation;

import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

/**
 * 代表用户必须与当前登陆的用户一致
 * 相当于使用Spring Security的@PreAuthorize("#{user.name == authentication.name}")的验证
 **/
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE})
@Constraint(validatedBy = AccountValidation.AuthenticatedAccountValidator.class)
public @interface AuthenticatedAccount {
    String message() default "不是当前登陆用户";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

```java
package tech.tystnad.domain.account.validation;

import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;


/**
 * 代表一个用户在数据仓库中是存在的
 **/
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE})
@Constraint(validatedBy = AccountValidation.ExistsAccountValidator.class)
public @interface ExistsAccount {
    String message() default "用户不存在";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

```java
package tech.tystnad.domain.account.validation;

import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

/**
 * 表示一个用户的信息是无冲突的
 * <p>
 * “无冲突”是指该用户的敏感信息与其他用户不重合，譬如将一个注册用户的邮箱，修改成与另外一个已存在的注册用户一致的值，这便是冲突
 **/
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE})
@Constraint(validatedBy = AccountValidation.NotConflictAccountValidator.class)
public @interface NotConflictAccount {
    String message() default "用户名称、邮箱、手机号码与现存用户产生重复";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

```java
package tech.tystnad.domain.account.validation;

import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

/**
 * 表示一个用户是唯一的
 * <p>
 * 唯一不仅仅是用户名，还要求手机、邮箱均不允许重复
 **/
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE})
@Constraint(validatedBy = AccountValidation.UniqueAccountValidator.class)
public @interface UniqueAccount {
    String message() default "用户名称、邮箱、手机号码均不允许与现存用户重复";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

## 自定义注解校验器

```java
package tech.tystnad.domain.account.validation;

import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;
import org.springframework.security.core.context.SecurityContextHolder;
import tech.tystnad.domain.account.Account;
import tech.tystnad.domain.account.AccountRepository;
import tech.tystnad.domain.auth.AuthenticAccount;

import java.lang.annotation.Annotation;
import java.util.Collection;
import java.util.function.Predicate;

/**
 * 用户对象校验器
 * <p>
 * 如，新增用户时，判断该用户对象是否允许唯一，在修改用户时，判断该用户是否存在
 **/
public class AccountValidation<T extends Annotation> implements ConstraintValidator<T, Account> {

    protected final AccountRepository repository;

    public AccountValidation(AccountRepository repository) {
        this.repository = repository;
    }

    protected Predicate<Account> predicate = c -> true;

    @Override
    public boolean isValid(Account value, ConstraintValidatorContext context) {
        // 在JPA持久化时，默认采用Hibernate实现，插入、更新时都会调用BeanValidationEventListener进行验证
        // 而验证行为应该尽可能在外层进行，Resource中已经通过@Vaild注解触发过一次验证，这里会导致重复执行
        // 正常途径是使用分组验证避免，但@Vaild不支持分组，@Validated支持，却又是Spring的私有标签
        // 另一个途径是设置Hibernate配置文件中的javax.persistence.validation.mode参数为“none”，这个参数在Spring的yml中未提供桥接
        // 为了避免涉及到数据库操作的验证重复进行，在这里做增加此空值判断，利用Hibernate验证时验证器不是被Spring创建的特点绕开
        return repository == null || predicate.test(value);
    }

    public static class ExistsAccountValidator extends AccountValidation<ExistsAccount> {
        public ExistsAccountValidator(AccountRepository repository) {
            super(repository);
        }

        public void initialize(ExistsAccount constraintAnnotation) {
            predicate = c -> repository.existsById(c.getId());
        }
    }

    public static class AuthenticatedAccountValidator extends AccountValidation<AuthenticatedAccount> {
        public AuthenticatedAccountValidator(AccountRepository repository) {
            super(repository);
        }

        public void initialize(AuthenticatedAccount constraintAnnotation) {
            predicate = c -> {
                Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
                if ("anonymousUser".equals(principal)) {
                    return false;
                } else {
                    AuthenticAccount loginUser = (AuthenticAccount) principal;
                    return c.getId().equals(loginUser.getId());
                }
            };
        }
    }

    public static class UniqueAccountValidator extends AccountValidation<UniqueAccount> {
        public UniqueAccountValidator(AccountRepository repository) {
            super(repository);
        }

        public void initialize(UniqueAccount constraintAnnotation) {
            predicate = c -> !repository.existsByUsernameOrEmailOrTelephone(c.getUsername(), c.getEmail(), c.getTelephone());
        }
    }

    public static class NotConflictAccountValidator extends AccountValidation<NotConflictAccount> {
        public NotConflictAccountValidator(AccountRepository repository) {
            super(repository);
        }

        public void initialize(NotConflictAccount constraintAnnotation) {
            predicate = c -> {
                Collection<Account> collection = repository.findByUsernameOrEmailOrTelephone(c.getUsername(), c.getEmail(), c.getTelephone());
                // 将用户名、邮件、电话改成与现有完全不重复的，或者只与自己重复的，就不算冲突
                return collection.isEmpty() || (collection.size() == 1 && collection.iterator().next().getId().equals(c.getId()));
            };
        }
    }

}
```

## 控制层使用

```java
package tech.tystnad.application;

import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;
import tech.tystnad.domain.account.Account;
import tech.tystnad.domain.account.AccountRepository;
import tech.tystnad.infrastructure.utility.Encryption;
import tech.tystnad.infrastructure.utility.SnowFlake;

import java.util.Objects;


/**
 * 用户资源的应用服务接口
 **/
@Service
@Transactional
public class AccountApplicationService {

    private final AccountRepository repository;

    private final Encryption encoder;

    private final SnowFlake snowFlake = new SnowFlake(1, 1);

    public AccountApplicationService(AccountRepository repository, Encryption encoder) {
        this.repository = repository;
        this.encoder = encoder;
    }

    public Account createAccount(Account account) {
        account.setId(snowFlake.nextId());
        account.setPassword(encoder.encode(account.getPassword()));
        if (Objects.isNull(account.getEnabled())) {
            account.setEnabled(true);
        }
        return repository.save(account);
    }

    public Account findAccountByUsername(String username) {
        if (!repository.existsByUsername(username)) {
            throw new NullPointerException("用户 " + username + " 信息不存在");
        }
        return repository.findByUsername(username);
    }

    public Account updateAccount(Account account) {
        return repository.save(account);
    }

}
```

`@Validated`在方法中声明只是用于标记校验的分组以及启用`@Valid`的数据校验，
自定义的注解校验（实现了`ConstraintValidator`接口）仍然需要在类上添加`@Validated`来生效

```java
package tech.tystnad.resource;

import jakarta.validation.Valid;
import org.springframework.cache.annotation.CacheConfig;
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;
import tech.tystnad.application.AccountApplicationService;
import tech.tystnad.domain.account.Account;
import tech.tystnad.domain.account.validation.AuthenticatedAccount;
import tech.tystnad.domain.account.validation.NotConflictAccount;
import tech.tystnad.domain.account.validation.UniqueAccount;


/**
 * 用户资源
 * <p>
 * 对客户端以Restful形式暴露资源，提供对用户资源{@link Account}的管理入口
 **/
@RestController
@RequestMapping("/accounts")
@Validated
@CacheConfig(cacheNames = "resource.account")
public class AccountResource {

    private final AccountApplicationService service;

    public AccountResource(AccountApplicationService service) {
        this.service = service;
    }

    /**
     * 根据用户名称获取用户详情
     */
    @GetMapping("/{username}")
    @Cacheable(key = "#username", unless = "#result == null")
    public Account getUser(@PathVariable("username") String username) {
        return service.findAccountByUsername(username);
    }

    /**
     * 创建新的用户
     */
    @PostMapping
    @CacheEvict(key = "#user.username")
    public Account createUser(@Valid @UniqueAccount @RequestBody Account user) {
        return service.createAccount(user);
    }

    /**
     * 更新用户信息
     */
    @PutMapping
    @CacheEvict(key = "#user.username")
    public Account updateUser(@Valid @AuthenticatedAccount @NotConflictAccount @RequestBody Account user) {
        return service.updateAccount(user);
    }
}
```

Last Modified 2023-03-14
