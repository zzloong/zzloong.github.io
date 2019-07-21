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

[MyBatis 官网](http://www.mybatis.org/mybatis-3/zh/index.html) 是这么介绍它自己的：

> MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

<!-- more -->

## 示例代码

- [awesome-spring-boot-examples](https://github.com/Michael728/awesome-spring-boot-examples)

## 依赖

这里仅展示和 MyBatis 相关的数据库依赖项，完整的示例，在文末会附上项目代码链接。

```java
<!--mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
```

`spring-boot-start` 系列的包，真是给 Spring Boot 开发带来了极大的便利，它的项目地址是：

- [Github-mybatis/spring-boot-starter](https://github.com/mybatis/spring-boot-starter)
- [官宣-What is MyBatis-Spring-Boot-Starter?](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/) 推荐

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


-- ----------------------------
-- Records of sys_user
-- ----------------------------
INSERT INTO `users` VALUES (1,'michael翔', '123', 'MAN', 'zx');
INSERT INTO `users` VALUES (2,'张小敬', '123', 'MAN', 'zxj');
INSERT INTO `users` VALUES (3,'李司辰', '123', 'MAN', 'lsc');
INSERT INTO `users` VALUES (4,'崔器', '123', 'MAN', 'cq');
INSERT INTO `users` VALUES (5,'姚汝能', '123', 'MAN', 'yrn');
INSERT INTO `users` VALUES (null,'檀棋', '123', ' WOMAN', 'tq');
INSERT INTO `users` (`userName`,`passWord`,`user_sex`,`nick_name`) VALUES ('michael', '123', 'MAN', 'zx');
```

说明：

- id 设置的是自增，当插入数据时，可以不传或者传 `null` 值，都可以；
- 插入数据，可以指定 `column` 也可以不指定；

`application-dev.properties`：

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

在很多 `Select` 语句需要做结果映射时，自然是相当麻烦。除了上面配置「驼峰属性自动映射」，也可以用在 `@Results` 中使用 `id` 来标识一个映射关系，然后可以用 `@ResultMap` 复用这个映射关系：

```java
@Select("SELECT * FROM users")
@Results(id = "user", value = {
        @Result(property = "userSex", column = "user_sex"),
        @Result(property = "nickName", column = "nick_name")
})
@Select("SELECT * FROM users WHERE id = #{id}")
List<UserEntity> getAll();

@ResultMap("user")
@Select("SELECT * FROM users WHERE id = #{id}")
UserEntity getUserById(Integer id);
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
    @Options(useGeneratedKeys = true, keyProperty = "id")
//   @SelectKey(statement = "select last_insert_id()", keyProperty = "id", before = false, resultType = Integer.class)
    void insert(UserEntity user);

    @Update("UPDATE users SET userName=#{userName},nick_name=#{nickName} WHERE id = #{id}")
    void update(UserEntity user);

    @Delete("DELETE FROM users WHERE id= #{id}")
    void deleteUserById(Long id);
}
```

说明：

- `insert` 这里用了一个 `@Options` 的注解，实现了「主键回填」的功能，也就是说，再创建好一个 `user` 之后，`user` 请求体中的 `id` 属性会自动赋值好；
- `@SelectKey` 注解被注释掉了，这个注解也同样可以实现「主键回填」的功能；

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
    public String save(UserEntity user) {
        userService.insert(user);
        // 用到了 主键回填 的配置
        return "Create success, user id: " + user.getId();
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
pageSizeZero=true
```

分页插键参数介绍：

- `helperDialect`：分页插件会自动检测当前的数据库链接，自动选择合适的分页方式
- `reasonable`：分页合理化参数，默认值为 false。当该参数设置为 true 时，`pageNum<=0` 时会查询第一页， `pageNum>pages`（超过总数时），会查询最后一页。默认 false 时，直接根据参数进行查询
- `params`：为了支持 `startPage(Object params)` 方法，增加了该参数来配置参数映射，用于从对象中根据属性名取值， 可以配置 `pageNum,pageSize,count,pageSizeZero,reasonable`，不配置映射的用默认值， 默认值为 `pageNum=pageNum;pageSize=pageSize;count=countSql;reasonable=reasonable;pageSizeZero=pageSizeZero`。
- `supportMethodsArguments`：支持通过 Mapper 接口参数来传递分页参数，默认值 false，分页插件会从查询方法的参数值中，自动根据上面 `params` 配置的字段中取值，查找到合适的值时就会自动分页
- `pageSizeZero`：默认值为 false，当该参数设置为 true 时，如果 `pageSize=0` 或者 `RowBounds.limit = 0` 就会查询出全部的结果（相当于没有执行分页查询，但是返回结果仍然是 Page 类型）。我测试时，发现不设置，`pageSize=0` 也会返回全部；

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

注意点：

- `PageHelper.startPage(pageNo,pageSize);` 只对其后的第一个查询有效；

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

- `offset`：和 `pageNum` 意思一样，指定返回记录的开始位置；
- `limit`：和 `pageSize` 意思一样，指定返回记录的数量；

### 进一步

上面的分页结果返回的内容有点多，一些属性并不想放在返回体中。可以进一步优化。编写工具类限定关心的属性。

分页查询结果封装类：

```java
@Data
public class PageResult {
    /**
     * 当前页码
     */
    private int pageNum;
    /**
     * 每页数量
     */
    private int pageSize;
    /**
     * 记录总数
     */
    private long totalSize;
    /**
     * 页码总数
     */
    private int totalPages;
    /**
     * 数据模型
     */
    private List<?> content;
}
```

分页查询工具类：

```java
public class PageUitls {
    /**
     * 将分页信息封装到统一的接口
     *
     * @param pageInfo
     * @return
     */
    public static PageResult getPageResult(PageInfo<?> pageInfo) {
        PageResult pageResult = new PageResult();
        pageResult.setPageNum(pageInfo.getPageNum());
        pageResult.setPageSize(pageInfo.getPageSize());
        pageResult.setTotalSize(pageInfo.getTotal());
        pageResult.setTotalPages(pageInfo.getPages());
        pageResult.setContent(pageInfo.getList());
        return pageResult;
    }
}
```

接口方法：

```java
/**
  * 查询所有用户
  *
  * @return
  */
PageResult getAll(int pageNum, int pageSize);
```

接口实现类：

```java
@Override
public PageResult getAll(int pageNum, int pageSize) {
    //将参数传给这个方法就可以实现物理分页了，非常简单。
    PageHelper.startPage(pageNum, pageSize);
    List<UserEntity> users = userMapper.getAll();
    PageInfo<UserEntity> pageInfo = new PageInfo<>(users);
    return PageUitls.getPageResult(pageInfo);
}
```

这样改写后，返回体就简洁许多了：

```shell
{
  "pageNum": 1,
  "pageSize": 2,
  "totalSize": 3,
  "totalPages": 2,
  "content": [
    {
      "id": 1,
      "userName": "Michael翔",
      "passWord": "123",
      "userSex": "MAN",
      "nickName": "ZX"
    },
    {
      "id": 2,
      "userName": "HQH",
      "passWord": "123",
      "userSex": "WOMAN",
      "nickName": "QQ"
    }
  ]
}
```

## IN 查询

关于 MyBatis 的 IN 查询，也是试验了很久，才 OK 的。 StackOverflow 上就有类似的问题 [How to use Annotations with iBatis (myBatis) for an IN query?](https://stackoverflow.com/questions/3428742/how-to-use-annotations-with-ibatis-mybatis-for-an-in-query)。

为了测试 MyBatis IN 查询，我们将之前的根据 ID 查询用户信息的接口进行修改，让它支持根据输入的 ID 列表查询多用户信息。

controller：

```java
@ApiOperation(value = "查询指定 ID 的用户", notes = "根据用户 id 列表查询其信息")
@ApiImplicitParam(name = "ids", value = "用户 id 列表", paramType = "path", required = true)
@GetMapping(value = "/user/{ids}")
public PageResult getUser(@RequestParam(name = "pageNum", defaultValue = "1", required = false) int pageNum,
                          @RequestParam(name = "pageSize", defaultValue = "2", required = false) int pageSize,
                          @PathVariable String ids) {
    List<String> idLst = Arrays.asList(ids.split(","));
    PageResult user = userService.getUserById(pageNum, pageSize, idLst);
    return user;
}
```

这里有个小注意点，`@ApiImplicitParam` 注解中的 `paramType = "path"` 记得修改为 `path`，因为请求参数中包含路径变量了，否则渲染 URL 时，会出问题。

mapper 类：

```java
@Select({
        "<script>",
        "SELECT * ",
        "FROM users WHERE id IN",
        "<foreach item='id' index='index' collection='ids' open='(' separator=',' close=')'>",
        "#{id}",
        "</foreach>",
        "</script>"
})
List<UserEntity> getUserById(@Param("ids") List<String> ids);
```

说明：

- `item` 标识集合中每一个元素进行迭代是的别名，很多教程中设置为 `item`，我这里改为 `id` 也是 OK，而且也易于理解；
- `index` 指定一个名字，用于表示在迭代过程中，每次迭代到的位置，从 0 开始；
- `open` 表示该语句以什么开始；
- `separator` 表示在每次进行迭代之间以什么符号作为分隔符；
- `close` 表示以什么结束；
- `collection` 属性，该属性是必须指定的，但是在不同情况下，该属性的值是不一样的；
- `@Param` 的设置比较关键，相当于给其修饰的参数指定一个别名：
  - 使用 `@Param`，默认会和参数名同名，或者以注解传入的变量名为准。变量名将作为 `@Select` 中的可用参数，比如，我这里这样定义 `@Param("ids2") List<String> ids`，那么，`@Select` 中可用参数名将是 `ids2`，`collection` 也须定义为 `ids2`，否则会报错：`nested exception is org.apache.ibatis.binding.BindingException: Parameter 'list' not found. Available parameters are [ids2, param1]`;
  - 不使用 `@Param` 时，那么，此时 `collection` 需要定义为 `list`，否则会报错：`nested exception is org.apache.ibatis.binding.BindingException: Parameter 'list2' not found. Available parameters are [collection, list]`;

上面的说明参考自 [mybatis查询sql中in条件使用(foreach)](https://blog.csdn.net/aiyawalie/article/details/52954138) ，没有找到官方文档支撑，待补充。

## 动态 SQL

待后续补充

- [spring boot(8)-mybatis三种动态sql](https://blog.csdn.net/wangb_java/article/details/73657958)
- [MyBatis注解应用之动态SQL语句](https://blog.csdn.net/owen_william/article/details/51815506)

## FAQ

### MyBatis 中 # 和 $ 的区别

- 简单说 `#{}` 是经过预编译的，是安全的，而 `${}` 是未经过预编译的，仅仅是取变量的值，是非安全的，存在 SQL 注入。`${}` 将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号。
- 使用 `${}` 的情况，`order by`、`like` 语句只能用 `${}`,用 `#{}` 会多个 `' '` 导致 SQL 语句失效。此外动态拼接 SQL，模糊查询时也要用 `${}`。

## 参考

- [纯洁的微笑-Spring Boot(六)：如何优雅的使用 Mybatis](http://www.ityouknow.com/springboot/2016/11/06/spring-boot-mybatis.html) 本文的主要参考文章之一，入门挺好
- [CSDN-larger5-[增删改查] SpringBoot + MyBatis（注解版）](https://blog.csdn.net/larger5/article/details/79616058) 这位博主的示例，代码结构和风格都比较规范，值得学习
- [CSDN-LuisChen的博客-Spring boot Mybatis 整合（完整版）](https://blog.csdn.net/Winter_chen001/article/details/77249029)
- [博客园-Ruthless-SpringBoot+Mybatis+Pagehelper分页](https://www.cnblogs.com/linjiqin/p/9687491.html) 查询结果关系映射那块，该文章介绍的 `@ResultMap` 比较方便；

分页

- [CSDN-SpringBoot使用Mybatis注解开发教程-分页-动态sql](https://blog.csdn.net/kingboyworld/article/details/78948304) 分页参考
- [博客园-朝雨忆轻尘-Spring Boot：实现MyBatis分页](https://www.cnblogs.com/xifengxiaoma/p/11027551.html) 推荐，`PageResult` 的优化，参考此文
- [PageHelper-官宣-如何使用分页插件](https://pagehelper.github.io/docs/howtouse/)

FAQ

- [知乎——这也许是你不曾留意过的 Mybatis 细节](https://zhuanlan.zhihu.com/p/47416328)