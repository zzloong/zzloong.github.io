---
title: Spring Boot 实战 —— MyBatis（注解版）使用方法
date: 2019-07-20 12:49:10
tags:
  - Spring Boot
  - Java
  - Data Base
categories:
  - Java
keywords:
  - Spring Boot
  - MyBatis
---

![MyBatis](https://b2.bmp.ovh/imgs/2019/07/5f37fc105fab759c.jpeg)

## 简介

MyBatis 是目前国内互联网公司用的比较多的框架。ORM 框架就是用来简化编程中操作数据库的代码的。

- Hibernate 宣称不用写一句 SQL，都是用 Java 来生成 SQL。Spring Data JPA 这种模式，基本上根据方法名就可以生成对应的 SQL 了。
- MyBatis 可以灵活调试动态 SQL。MyBatis 初期使用比较麻烦，需要各种配置。后来可以使用注解方式简化使用配置。

## 依赖

这里仅展示和 MyBatis 相关的数据库依赖项，完整的示例，在文末会附上项目代码链接。

```java
<!--mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>
```

## 配置

创建 `users` 表的 SQL：

```SQL
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for `users`
-- ----------------------------
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '主键id',
  `userName` varchar(32) DEFAULT NULL COMMENT '用户名',
  `passWord` varchar(32) DEFAULT NULL COMMENT '密码',
  `user_sex` varchar(32) DEFAULT NULL,
  `nick_name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=28 DEFAULT CHARSET=utf8;
```

application-dev.properties：

```shell
swagger.enable=true
server.port=8081


spring.datasource.url=jdbc:mysql://192.168.3.43:3306/beta?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=true
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

mybatis.type-aliases-package==com.michael.springbootmybatis.model
mybatis.configuration.map-underscore-to-camel-case=true
```

- 配置驼峰属性自动映射，例如实体中属性为 `userSex`,数据库属性为 `user_sex`，MyBatis 默认是不能自动转换的。我们可以配置 `mybatis.configuration.map-underscore-to-camel-case` 实现自动映射。如果不进行此配置，通常我们要自定义以下结果集映射：

```java
@Results({
        @Result(property = "userSex", column = "user_sex"),
        @Result(property = "nickName", column = "nick_name")
})
@Select("SELECT * FROM users WHERE id = #{id}")
UserEntity getUserById(Long id);
```

## 代码

这里仅展示关键的部分的代码，完整可看下文的示例代码。

实体类：

```java
@Data
@ApiModel(description = "UserEntity 实体类")
public class UserEntity implements Serializable {
    private static final long serialVersionUID = 1L;
    @ApiModelProperty(value = "用户 id", dataType = "Long")
    private Long id;
    @ApiModelProperty(value = "用户名", required = true)
    private String userName;
    @ApiModelProperty(value = "密码")
    private String passWord;
    @ApiModelProperty(value = "性别")
    private UserSexEnum userSex;
    @ApiModelProperty(value = "昵称")
    private String nickName;


    @Override
    public String toString() {
        return "userName " + this.userName + ", password " + this.passWord + " , sex " + this.userSex;
    }

}
```

`dao/mapper` 接口，数据库交互（`Data Access Object`）层：

```java
public interface UserMapper {
//    @Results({
//            @Result(property = "userSex", column = "user_sex", javaType = UserSexEnum.class),
//            @Result(property = "nickName", column = "nick_name")
//    })
    @Select("SELECT * FROM users")
    Page<UserEntity> getAll();

//    @Results({
//            @Result(property = "userSex", column = "user_sex"),
//            @Result(property = "nickName", column = "nick_name")
//    })
    @Select("SELECT * FROM users WHERE id = #{id}")
    UserEntity getUserById(Long id);

    @Insert("INSERT INTO users(userName, passWord, user_sex, nick_name) " +
            "VALUES(#{userName}, #{passWord}, #{userSex}, #{nickName})")
    void insert(UserEntity user);

    @Update("UPDATE users SET userName=#{userName},nick_name=#{nickName} WHERE id = #{id}")
    void update(UserEntity user);

    @Delete("DELETE FROM users WHERE id= #{id}")
    void deleteUserById(Long id);
}
```

service 接口：

```java
public interface UserService {

    /**
     * 查询所有用户
     *
     * @return
     */
    Map<String,Object> getAll(int pageNum, int pageSize);

    /**
     * 根据用户 ID 查询用户
     *
     * @param id 用户 ID
     * @return
     */
    UserEntity getUserById(Long id);

    /**
     * 新增一个用户
     *
     * @param user
     */
    void insert(UserEntity user);

    /**
     * 更新用户信息，用户 ID 不传，会更新失败
     *
     * @param user
     */
    String update(UserEntity user);

    /**
     * 根据用户 ID 删除用户
     *
     * @param id
     */
    String deleteById(Long id);

}
```

service 接口的实现类：

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public Map<String,Object> getAll(int pageNum, int pageSize) {
        //将参数传给这个方法就可以实现物理分页了，非常简单。
        PageHelper.startPage(pageNum, pageSize);
        PageInfo<UserEntity> pageInfo = new PageInfo<>(userMapper.getAll());
        Long total = pageInfo.getTotal();
        List<UserEntity> users = pageInfo.getList();
        Map<String,Object> map = new HashMap<>();
        map.put("total", total);
        map.put("data", users);
        return map;
    }

    @Override
    public UserEntity getUserById(Long id) {
        return userMapper.getUserById(id);
    }

    @Override
    public void insert(UserEntity user) {
        userMapper.insert(user);
    }

    @Override
    public String update(UserEntity user) {
        userMapper.update(user);
        return "success";
    }

    @Override
    public String deleteById(Long id) {
        userMapper.deleteUserById(id);
        return "success";
    }

}
```

controller 类：

```java
@RestController
@RequestMapping("/api/v1/")
@Api(tags = {"用户相关接口"}, value = "用户模块")
public class UserController {
    @Autowired
    private UserService userService;

    /**
     * 查询全部用户
     *
     * @return
     */
    @ApiOperation(value = "获取用户列表", notes = "获取全部用户信息")
    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public Map<String,Object> getUsers(
            @RequestParam(name = "pageNum", defaultValue = "1", required = false) int pageNum,
            @RequestParam(name = "pateSize", defaultValue = "2", required = false) int pageSize) {
        return userService.getAll(pageNum, pageSize);
    }

    /**
     * 根据用户 ID 查询用户
     *
     * @param id
     * @return
     */
    @ApiOperation(value = "查询单用户", notes = "根据用户id 查询其信息")
    @ApiImplicitParam(name = "id", value = "用户id", paramType = "query", required = true)
    @GetMapping("/user/{id}")
    public UserEntity getUser(Long id) {
        UserEntity user = userService.getUserById(id);
        return user;
    }

    /**
     * 存储用户信息
     *
     * @param user
     */
    @ApiOperation(value = "存储用户信息", notes = "存储用户详细信息")
    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public void save(UserEntity user) {
        userService.insert(user);
    }

    /**
     * 更新用户信息
     *
     * @param user
     */
    @ApiOperation(value = "更新用户信息", notes = "更新用户的个人信息")
    @PutMapping("/user/")
    public void update(@RequestBody UserEntity user) {
        userService.update(user);
    }

    /**
     * 根据用户 ID 删除用户
     *
     * @param id
     */
    @ApiOperation(value = "删除用户", notes = "根据用户id删除用户信息")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "用户id", required = true, paramType = "path")
    })
    @RequestMapping(value = "/user/{id}", method = RequestMethod.DELETE)
    public void delete(@PathVariable("id") Long id) {
        userService.deleteById(id);
    }
}
```

启动类：

```java
@SpringBootApplication
@MapperScan("com.michael.springbootmybatis.mapper")
public class SpringBootMybatisApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootMybatisApplication.class, args);
    }

}
```

- `@MapperScan("com.winter.mapper")` 这个注解非常的关键，这个对应了项目中 `mapper/dao` 所对应的包路径。
- 如果不用上面的方式，就需要在每个 `mapper/dao` 类上使用 `@Mapper` 注解；

## 分页

通常，在进行查询时，我们为了避免一次性返回所有结果，通常会进行分页。比如查询所有用户的接口，实际应用中，用户数据可能会很多，如果全部一次返回，明显不合适。这时候，就需要进行分页查询。

本文我们选用插键 `pagehelper-spring-boot-starter` 要进行分页。

### 添加依赖

```shell
<!-- 分页插件 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

### 分页配置

需要添加相应的配置：

```shell
#pagehelper分页插件
pagehelper.helperDialect=mysql
pagehelper.reasonable=true
pagehelper.supportMethodsArguments=true
pagehelper.params=count=countSql
pagehelper.row-bounds-with-count=true
```

### 代码变动

`mapper` 中查找全部用户的方法改成如下：

```java
@Select("SELECT * FROM users")
Page<UserEntity> getAll();
```

service 接口和其实现类的方法改成：

```java
PageInfo<UserEntity> getAll(int pageNum, int pageSize);
```

service 接口实现类：

```java
@Override
public PageInfo<UserEntity> getAll(int pageNum, int pageSize) {
    //将参数传给这个方法就可以实现物理分页了，非常简单。
    PageHelper.startPage(pageNum, pageSize);
    PageInfo<UserEntity> pageInfo = new PageInfo<>(userMapper.getAll());
    return pageInfo;
}
```

controller 类：

```java
@ApiOperation(value = "获取用户列表", notes = "获取全部用户信息")
@RequestMapping(value = "/users", method = RequestMethod.GET)
public PageInfo<UserEntity> getUsers(
        @RequestParam(name = "pageNum", defaultValue = "1", required = false) int pageNum,
        @RequestParam(name = "pateSize", defaultValue = "2", required = false) int pageSize) {
    return userService.getAll(pageNum, pageSize);
}
```

![分页](https://b2.bmp.ovh/imgs/2019/07/8114562151cbc00f.jpg)

除了上面的参数名，还习惯用下面的参数名：

- `offset`：指定返回记录的开始位置；
- `limit`：指定返回记录的数量；

## 示例代码

- [awesome-spring-boot-examples](https://github.com/Michael728/awesome-spring-boot-examples)

## 参考

- [纯洁的微笑-Spring Boot(六)：如何优雅的使用 Mybatis](http://www.ityouknow.com/springboot/2016/11/06/spring-boot-mybatis.html) 本文的主要参考文章之一
- [CSDN-larger5-[增删改查] SpringBoot + MyBatis（注解版）](https://blog.csdn.net/larger5/article/details/79616058) 这位博主的示例，代码结构和风格都比较规范，值得学习
- [CSDN-LuisChen的博客-Spring boot Mybatis 整合（完整版）](https://blog.csdn.net/Winter_chen001/article/details/77249029)
- [CSDN-SpringBoot使用Mybatis注解开发教程-分页-动态sql](https://blog.csdn.net/kingboyworld/article/details/78948304) 分页参考