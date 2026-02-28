# 阿里 Java 开发规范（参考阿里面向）

> 企业级 Java 开发标准

## 技术栈

### 后端

```
核心框架：Spring Boot 3.x
持久层：MyBatis-Plus / Spring Data JPA
缓存：Redis / Caffeine
消息：RocketMQ / Kafka
微服务：Spring Cloud Alibaba / Dubbo
配置：Nacos / Sentinel
安全：Spring Security
```

### 移动端

```
核心：Jetpack Compose + ViewModel
网络：Retrofit + OkHttp3 + Coil
图片：Glide / Coil
缓存：Room + SharedPreferences
数据库：Room / SQLite
依赖注入：Hilt
```

---

## 命名规范

### 严格的驼峰命名

| 类型 | 后缀 | 示例 |
|------|------|------|
| Entity | DO | `UserDO` |
| VO | VO | `UserVO` |
| Query | Query | `UserQuery` |
| Service | Impl | `UserServiceImpl` |

---

## 分层架构

```
Controller (控制层)
    ↓
Service (业务逻辑层)
    ↓
Manager/Repository (数据访问层)
    ↓
DAO (数据访问对象)
```

---

## 接口规范

### 接口幂等性

```java
public interface UserMapper {
    UserVO toVo(UserDO userDO);
}
```

---

## VO/DO 转换

```java
@Component
public class UserMapperImpl implements UserMapper {
    
    @Override
    public UserVO toVo(UserDO userDO) {
        UserVO vo = new UserVO();
        vo.setId(userDO.getId());
        vo.setName(userDO.getName());
        return vo;
    }
}
```

---

## 配置编码

### application.properties

```properties
# 服务器
server.port=8080

# 数据源
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.password=root

# JPA
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update

# MyBatis-Plus
mybatis-plus.mapper-locations=classpath:mapper/*.xml
mybatis-plus.type-aliases-package=com.example.entity.enums
```

---

## 测试规范

### 单元测试

```java
@SpringBootTest
class UserServiceTest {
    @Autowired
    private UserService userService;
    
    @Test
    void shouldCreateUser() {
        // ...
    }
}
```

---

## 线程

### 线程池配置

```yaml
spring:
  task:
    execution:
      pool:
        core-size: 8
        max-size: 16
        queue-capacity: 1000
        keep-alive: 60s
```

---

## AI 编码检查清单

- [ ] 遵循严格的驼峰命名
- [ ] 使用三层架构
- [ ] 使用 MyBatis-Plus
- [ ] 使用 VO/DO 转换
- [ ] 统一异常处理（Result + ErrorCode）
- [ ] 集成阿里组件
- [ ] 编写单元测试
- [ ] 遵循安全编码规范
