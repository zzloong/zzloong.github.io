---
title: Spring Boot 2.x 实战 —— 集成 Swagger 生成 RESTful API 文档
date: 2019-07-14 00:49:10
tags:
  - Java
  - Spring Boot
categories:
  - Java
keywords:
  - API
---

## 简介

[Swagger](https://swagger.io/) 官网是这么描述它的：`The Best APIs are Built with Swagger Tools`。

[Swagger](https://swagger.io/docs/specification/about/) 是一套基于 OpenAPI 规范构建的开源工具，可以帮助我们设计、构建、记录以及使用 Rest API。Swagger 主要包含了以下三个部分：

1. [Swagger Editor](https://swagger.io/tools/swagger-editor/)：基于浏览器的编辑器，我们可以使用它编写我们 OpenAPI 规范。
2. [Swagger UI](https://swagger.io/tools/swagger-ui/)：它会将我们编写的 OpenAPI 规范呈现为交互式的 API 文档，后文我将使用浏览器来查看并且操作我们的 Rest API。
3. [Swagger Codegen](https://swagger.io/tools/swagger-codegen/)：它可以通过为 OpenAPI（以前称为 Swagger）规范定义的任何 API 生成服务器存根和客户端 SDK 来简化构建过程。

Spring Boot 使得开发 RESTful 服务变得简单。那么编写 Spring Boot 接口，为何要用 Swagger 呢？

- 代码改变，文档就会改变。只需要少量的注释，Swagger 就可以根据代码自动生成 API 文档。
- Swagger UI 是一份交互式的 API 文档，可以直接在 Web 界面调用 API。这里有一份 Swagger UI 的 [Live Demo](https://petstore.swagger.io/)，看看官方是怎么写 RESTful API 的。

## 添加依赖

`pom.xml` 引入 Swagger 相关的依赖：

```shell
<!-- swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>${swagger.version}</version>
</dependency>
<!-- swagger2 ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>${swagger.version}</version>
</dependency>
```

使用 `property` 定义了 Swagger 的版本，因此还需要添加：

```shell
<swagger.version>2.9.2</swagger.version>
```

依赖说明：

- `springfox-swagger2` Swagger 的 Java 实现
- `springfox-swagger-ui` Swagger UI 页面的依赖

## Swagger 配置类

使用注解 `@Configuration` 编写 Swagger  配置类—— `SwaggerConfig`。

新建 `config` 的包，创建 `SwaggerConifg` 的配置类：

```java
//通过@Configuration注解，让Spring来加载该类配置
@Configuration
//通过@EnableSwagger2注解来启用Swagger2
@EnableSwagger2
//@ConditionalOnExpression 为Spring的注解，用户是否实例化本类，用于是否启用Swagger的判断（生产环境需要屏蔽Swagger）
@ConditionalOnExpression("${swagger.enable:true}")
public class SwaggerConfig {

    // select()函数返回一个ApiSelectorBuilder实例用来控制哪些接口暴露给Swagger来展现，本例采用指定扫描的包路径来定义，
    // Swagger会扫描该包下所有Controller定义的API，并产生文档内容（除了被@ApiIgnore指定的请求）
    @Bean
    public Docket createRestApi() {
        // apiInfo()用来创建该Api的基本信息（这些基本信息会展现在文档页面中
        ApiInfo apiInfo = new ApiInfoBuilder()
                .title("标题: Spring Boot 项目集成 Swagger 示例文档")
                .description("描述: 我的博客地址是 https://michael728.github.io")
                .termsOfServiceUrl("https://michael728.github.io/")
                .version("1.0")
                .build();

        Docket docket = new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo)
                // select()函数返回一个ApiSelectorBuilder实例
                .select()
                // 决定了暴露哪些接口给 Swagger
                .paths(regex("/api/.*"))
                .build()
                .useDefaultResponseMessages(false)
                .glo
        return docket;
    }
}
```

说明：

- `@Configuration` 是告诉 Spring Boot 需要加载这个配置类；
- `@EnableSwagger2` 是启用 Swagger2，没加的话，就看不到效果了；
- `ApiInfo` 对象用来设置一些文档的版本号、联系人邮箱、网站、版权、开源协议等等信息（这些基本信息会展现在文档页面中）。并使用 `Docket.apiInfo()` 方法来设置；
- `Docket` 上增加筛选。提供了 `apis()` 和 `paths()` 两个方法帮助我们在不同级别上过滤接口：
  - `apis()` 这种方式我们可以指定包名的方式，让 Swagger 只去某些包下面扫描；
  - `paths()` 这种方式可以通过筛选 API 的 url 来进行筛选；
- `@ConditionalOnExpression("${swagger.enable:true}")` 这个注解控制了是否启用 Swagger，我们需要在 `appplication.properties` 中加上 `swagger.enable=true`

## 编写控制器类——Controller 类

我们先介绍一下在用 Swagger 时的常用注解：

- `@API` 类的注解，可以给控制器增加描述和标签信息。用在请求的类上，代表了这个类是 Swagger 的资源
  - `tags`：控制器标签，对该类进行「分类」，参数是个字符串数组，如果配置了多个值，会在多个分类中看到；
  - `value`：该参数没什么意义，在 UI 界面上并不显示，可不用配置
- `@ApiModel` 类注解，对 API 涉及的对象做描述，可用于响应实体类，说明实体作用
  - `value` Model 展示时的名称，默认是 实体类的名称，比如 `UserEntity`；
  - `description` 实体类的描述

![model.jpg](https://i.loli.net/2019/07/13/5d29df5c8571185458.jpg)

类成员变量的的注解：

- `@ApiModelProperty` 用在实体类的属性上
  - `value` 属性字段描述；
  - `required` 参数是否必选；
  - `name` 重写字段名称；
  - `dataType` 重写字段类型；
  - `allowEmptyValue` 是否允许为空；
  - `allowbleValues` 该字段允许的值。当我们 API 某个参数为枚举类型时，使用这个参数就可以清楚高速 API 使用者能允许传入的值

方法的注解：

- `@ApiOperation` 描述方法的用途，用来展开对接口的描述
  - `value` 接口简要描述；
  - `notes` 接口发布说明，详细描述；
- `@ApiImplicitParams` 用于描述接口的非对象参数集，一般与 `@ApiImplicitParams` 组合使用
- `@ApiImplicitParam` 描述参数信息
  - `value` 参数意义的描述
  - `name` 参数名字；
  - `required` 默认 `false`，参数是否必传
  - `dataType` 参数数据类型，只作为标志说明，并没有实际验证
    - `Long`
    - `String`
  - `paramType` 参数类型，表示参数放在哪里
    - `query`，默认值，`Query String` 的方式传参，请求参数的获取：`@RequestParam`
    - `path` 路径参数，请求参数的获取：`@PathVariable`
    - `header` 请求参数的获取：`@RequestHeader`
- `@PathVariable` 路径参数，给类似 `@GetMappIng("/user/{id}")` 参数通过路径传入

其他：

- `@ApiIgnore`：用于类或者方法上，屏蔽接口不被显示在页面上；
- `@Profile({"dev","test"})`：用于配置类上，表示对什么环境启用；
- `@ApiParam` 不能直接用在方法上，而是用在方法的形参定义中，下文会有示例；

实体类示例：

```java
@Data
@ApiModel(value = "用户实体")
public class UserEntity {
    public static final long serialVersionUID = 1L;
    @ApiModelProperty(value = "用户 id")
    private int id;
    @ApiModelProperty(value = "用户名", required = true)
    private String userName;
    @ApiModelProperty(value = "密码" )
    private String passWord;
    @ApiModelProperty(value = "性别")
    private UserSexEnum userSex;
    @ApiModelProperty(value = "昵称" )
    private String nickName;

    public UserEntity(Integer id, String userName, String passWord) {
        this.id = id;
        this.userName = userName;
        this.passWord = passWord;
    }
}
```

下面是一个控制类的示例：

```java
@RestController
@RequestMapping("/api/v1/")
@Api(tags = {"用户相关接口"}, value = "用户模块")
public class UserController {

    // 模拟数据库
    public static List<UserEntity> users = new ArrayList<>();

    static {
        UserEntity user1 = new UserEntity(1, "michael", "123");
        UserEntity user2 = new UserEntity(2, "qq", "123");
        UserEntity user3 = new UserEntity(3, "hh", "123");
        users.add(user1);
        users.add(user2);
        users.add(user3);
    }

    @ApiOperation(value = "获取用户列表", notes = "获取全部用户信息")
    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public List<UserEntity> getUsers() {
        return users;
    }

    @ApiOperation(value = "查询单用户", notes = "根据用户id 查询其信息")
    @ApiImplicitParam(name = "id", value = "用户id", paramType = "query", required = true)
    @GetMapping("/user/{id}")
    public UserEntity getUser(@PathParam("id") int id) {
        UserEntity user = users.get(id);
        return user;
    }

    @ApiOperation(value = "存储用户信息", notes = "存储用户详细信息")
    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public UserEntity saveUser(@ApiParam(value = "用户信息", required = true)
                               @RequestBody UserEntity user) {
        users.add(user);
        return user;
    }

    @ApiOperation(value = "删除用户", notes = "根据用户id删除用户信息")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "id", value = "用户id", required = true, paramType = "path")
    })
    @RequestMapping(value = "/user/{id}", method = RequestMethod.DELETE)
    public int deleteUser(@PathVariable("id") int id) {
        users.remove(id);
        return id;
    }


    @ApiOperation(value = "更新用户信息", notes = "更新用户的个人信息")
    @PutMapping("/user/")
    public UserEntity updateUser(@RequestBody UserEntity user) {
        int id = user.getId();
        UserEntity oldUser = users.get(id);
        users.set(id, user);
        return user;
    }
}
```

## 启动

启动应用，访问 `localhost:8080/swagger-ui.html` 可以访问到 `Swagger UI`，可以点击 `Try it out` 按钮，调用 API：

![Jietu20190713-211403-swagger.jpg](https://i.loli.net/2019/07/13/5d29d9419011131674.jpg)

页面上还会有一个 Models 的分类。Swagger UI 会根据我们在实体上使用的 `@ApiModel` 和 `@ApiModelProperty` 注解来自动补充实体以及其属性的描述和备注。

![model.jpg](https://i.loli.net/2019/07/13/5d29df5c8571185458.jpg)

## 示例代码

- [awesome-spring-boot-examples](https://github.com/Michael728/awesome-spring-boot-examples)

## One More Thing

### Web API 的风格

开发 API，先了解一下有哪些 Web API 的风格吧：

- RPC：RPC 面向过程，RPC 形式的 API 组织形态是类和方法，API 的命名往往是一个动词，比如 `GetUserInfo,CreateUser`;
- REST：REST 面向资源，也是下文将要介绍的一种 API 风格；
- GraphQL：就是面向数据查询，采用GraphQL，甚至不需要有任何的接口文档，在定义了Schema之后，服务端实现Schema，客户端可以查看Schema，然后构建出自己需要的查询请求来获得自己需要的数据

### REST

上文提到了 RESTful API 的概念，我觉得，不如趁机了解一下。因为在实际的项目中发现，并不是每个 Spring Boot 的开发人员都能意识到开发的 API 要尽量符合 RESTful 规则的。REST 实际上只是一种设计风格，它并不是标准。

术语：

- `Endpoint` 终点，可以理解为路径，表示 API 的具体网址。
- `API（Application Programming Interface)`，应用程序编程接口
- `REST` 是 `Representational State Transfer` 的缩写。如果一个架构符合 REST 原则，就称它为 RESTful 架构。RESTful API 就是 REST 风格的 API
  - Resource：资源，即数据。
  - Representational：某种表现形式，比如用 JSON，XML，JPEG 等；
  - State Transfer：状态变化。通过 HTTP 动词实现

在 RESTful 架构中，每个网址代表一种资源（`resource`），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。

### 资源的操作

RESTful 的核心思想就是，客户端发出的数据操作指令都是"动词 + 宾语"的结构。比如，`GET /articles` 这个命令，`GET` 是动词，`/articles` 是宾语。

对于资源的具体操作类型，由 HTTP 动词表示（括号里是对应的 SQL 命令）：

- GET（SELECT）：从服务器取出资源（一项或多项）。
- POST（CREATE）：在服务器新建一个资源。
- PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：从服务器删除资源

还有两个不常用的 HTTP 动词：

- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的

[知乎](https://www.zhihu.com/question/28557115)上的一个回答，我觉得很精辟：

- 看 Url 就知道要什么
- 看 http method 就知道干什么
- 看 http status code 就知道结果如何

一位[答主](https://www.zhihu.com/question/28557115/answer/47846156)给出的示例：

```java
GET /rest/api/getDogs --> GET /rest/api/dogs 获取所有小狗狗
GET /rest/api/addDogs --> POST /rest/api/dogs 添加一个小狗狗
GET /rest/api/editDogs/:dog_id --> PUT /rest/api/dogs/:dog_id 修改一个小狗狗
GET /rest/api/deleteDogs/:dog_id --> DELETE /rest/api/dogs/:dog_id 删除一个小狗狗
```

### 信息过滤 Filtering

如果记录数量很多，服务器不可能都将它们返回给用户。API 应该提供参数，过滤返回结果

- `?limit=10`：指定返回记录的数量
- `?offset=10`：指定返回记录的开始位置。
- `?page=2&per_page=100`：指定第几页，以及每页的记录数

## 参考

- [CSDN-Spring Boot集成Swagger](https://blog.csdn.net/q547550831/article/details/79833569)
- [官宣-Swagger](https://swagger.io/docs/)
- [IBM-在 Spring Boot 项目中使用 Swagger 文档](https://www.ibm.com/developerworks/cn/java/j-using-swagger-in-a-spring-boot-project/index.html)
- [蜻蜓HTTP-springboot 集成完整的swagger2](https://www.qingtingip.com/h_341568.html)

API 介绍

- [阮一峰-RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
- [阮一峰-RESTful API 最佳实践](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)
- [segmentfault-Philipp Hauer-[译]RESTful API 设计最佳实践](https://segmentfault.com/a/1190000011516151) 推荐
- [RESTful Service API 设计最佳工程实践和常见问题解决方案](https://www.macode.net/restful-service-api-design-best-practise/) 总结的相当好，博客值得阅读
- [华为云-API设计中关于RPC和REST 两种风格选择的个人理解](https://bbs.huaweicloud.com/blogs/691955e0f7b111e8bd5a7ca23e93a891)
- [跟着 Github 学习 Restful HTTP API 设计](https://cizixs.com/2016/12/12/restful-api-design-guide/)
- [梁桂钊——人人都是 API 设计师：我对 RESTful API、GraphQL、RPC API 的思考](http://blog.720ui.com/2019/api_design_talk/) 作者分享了一些阿里团队里做法
- [阿里研究员谷朴：API 设计最佳实践的思考](https://yq.aliyun.com/articles/683044)
- [朱晔的互联网架构实践心得S2E5：浅谈四种API设计风格（RPC、REST、GraphQL、服务端驱动）](https://www.cnblogs.com/lovecindywang/p/10383756.html)
