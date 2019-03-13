Spring Boot - 构建优雅的Restful API

什么是Restful
REST（英文：Representational State Transfer，简称REST）描述了一个架构样式的网络系统，比如 web 应用程序。它首次出现在 2000 年 Roy Fielding 的博士论文中，他是 HTTP 规范的主要编写者之一。在目前主流的三种Web服务交互方案中，REST相比于SOAP（Simple Object Access protocol，简单对象访问协议）以及XML-RPC更加简单明了，无论是对URL的处理还是对Payload的编码，REST都倾向于用更加简单轻量的方法设计和实现。值得注意的是REST并没有一个明确的标准，而更像是一种设计的风格。-- 摘自百度百科

环境工具
JDK1.8
MySQL5.7
Postman
IDEA
Maven3
当前使用的Spring Boot的版本为1.5.8.RELEASE

创建项目
IDEA -> New -> Project -> Spring Initializr -> 填写Group和Artifact -> Next -> 选择Web，勾选Web；选择SQL，勾选MyBatis和MySQL -> Next -> Finish

创建相应包、类和文件
需要创建一些包、类和相应的文件

com.roachfu.tutorial
    controller
        DemoController.java
    entity
        Demo.java
    mapper
        DemoMapper.java
    service
        impl
            DemoServiceImpl.java
        DemoService.java
    SpringBootRestfulApplication.java
Restful 风格
我们知道Http是有很多种请求方式的：
GET、HEAD、POST、PUT、PATCH、DELETE、OPTIONS、TRACE。

我们常用的有POST、DELETE、PUT、GET，分别对应我们的增删改查。

GET /users：获取用户列表

GET /users/{uid} 获取某个用户信息

POST /users 新增一个用户

PUT /users/{uid} 修改某个用户信息

DELETE /users/{uid} 删除某个用户



application.properties配置
设置了上下文路径，MyBatis配置以及连接MySQL数据库

server.context-path=/tutorial

mybatis.type-aliases-package=com.roachfu.tutorial.entity

mybatis.configuration.map-underscore-to-camel-case=true

spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://192.168.56.2:3306/spring_boot?useUnicode=true&characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=Fanshu_123456
DemoController
@RestController
@RequestMapping("/demos")
public class DemoController {

    @Resource
    private DemoService demoService;

    @PostMapping
    public int add(@RequestBody Demo demo){
        return demoService.save(demo);
    }

    @GetMapping
    public List<Demo> queryList(){
        return demoService.getList();
    }

    @GetMapping("/{id}")
    public Demo queryOne(@PathVariable("id")Integer id){
        return demoService.getOne(id);
    }

    @PutMapping("/{id}")
    public int update(@PathVariable("id")Integer id, @RequestBody Demo demo){
        demo.setId(id);
        demo.setUpdateTime(new Date());
        return demoService.update(demo);
    }

    @DeleteMapping("/{id}")
    public int delete(@PathVariable("id")Integer id){
        return demoService.delete(id);
    }
}
@GetMapping 等价于 @RequestMapping(method = RequestMethod.GET)
@GetMapping("/{id}") 等价于 @RequestMapping(value = "/{id}", method = RequestMethod.GET)
@PostMapping 等价于 @RequestMapping(method = RequestMethod.POST)
以此类推。

DemoServiceImpl
@Service
public class DemoServiceImpl implements DemoService {

    @Resource
    private DemoMapper demoMapper;

    @Override
    public List<Demo> getList() {
        return demoMapper.selectList();
    }

    @Override
    public Demo getOne(Integer id) {
        return demoMapper.selectOne(id);
    }

    @Override
    public int save(Demo demo) {
        return demoMapper.insert(demo);
    }

    @Override
    public int update(Demo demo) {
        return demoMapper.update(demo);
    }

    @Override
    public int delete(Integer id) {
        return demoMapper.delete(id);
    }
}
至于entity，mapper等在前面的教程中已经有展示过了，也可以查看项目源码

演示结果
由于直接在浏览器中输入URL，请求只支持GET请求，这样就无法看到其他请求方式的效果。
所以我们要借助Postman来进行测试。需要设置content-type为application/json

运行SpringBootRestfulApplication.java中的main方法。
打开Postman，并设置对应的方法，例如用POST请求新增一个Demo
新增一个Demo
测试其他的方法并查看结果
GET http://localhost:8080/tutorial/demos
GET http://localhost:8080/tutorial/demos/1
PUT http://localhost:8080/tutorial/demos/1
DELETE http://localhost:8080/tutorial/demos/1
好吧，优雅的Restful API 我们就搞定了
