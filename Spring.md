## Spring特性

### IoC和DI

#### IoC(Inversion of Control, 控制反转)
> 控制：IoC容器控制了对象——外部资源获取（包括文件等）<br>
> 反转：容器帮忙创建及注入依赖对象，对象只是被动的接受依赖对象

#### DI(Dependency Injection)
> 即“依赖注入”：由容器动态的将某个依赖关系注入到组件之中。

需要弄清楚以下问题
1. 谁依赖于谁：应用程序依赖于IoC容器；

2. 为什么需要依赖：应用程序需要IoC容器来提供对象需要的外部资源；

3. 谁注入谁：IoC容器注入应用程序某个对象，应用程序依赖的对象；

4. 注入了什么：就是注入某个对象所需要的外部资源（包括对象、资源、常量数据）。


### 插件[Lombok](https://projectlombok.org/features/all)简化代码的部分注解

+ `@NonNull` 可以标注在方法、字段、参数之上，表示对应的值不可以为空，这样就不会出现NullPointerException
+ `@Nullable` 可以标注在方法、字段、参数之上，表示对应的值可以为空
+ `@CleanUp` 自动资源管理：不用再在finally中添加资源的close方法
+ `@Setter/@Getter` 自动生成set和get方法
+ `@ToString` 自动生成toString方法
+ `@EqualsAndHashcode` 从对象的字段中生成hashCode和equals的实现
+ `@NoArgsConstructor`,`@RequiredArgsConstructor` 和 `@AllArgsConstructor`自动生成构造方法
+ `@Data` 自动生成set/get方法，toString方法，equals方法，hashCode方法，不带参数的构造方法
+ `@Value` 用于注解final类（不可变且不允许被继承）
+ `@Builder`  产生复杂的构建器api类
+ `@SneakyThrows` 异常处理（谨慎使用）
+ `@Synchronized` 同步方法安全的转化
+ `@Getter(lazy=true)`在用到cached的时候自动生成，同时，Lombok会自动管理线程安全的问题，不会重复赋值
+ `@Log` 支持各种logger对象，使用时用对应的注解放在类上，如@Log4j（@Log的变体），会得到一个静态的最终日志字段

附：[某博客](http://greeks.studio/tags/Lombok)关于Lombok几个注解的翻译

---

## SpringBoot

### CLI(Spring Boot Commad Line)
> 是一个命令行工具，可以用它来快速构建Spring原型应用。
  
windows下安装  
```bash
set PATH=D:\bin路径;%PATH%
set PATH=D:\release目录\spring-boot-cli-2.1.4.RELEASE\bin;%PATH%
```  

### Spring Boot Annotation

#### @RestController
>@RestController注解继承自@Controller<br>

1. 使用@RestController注解Controller，则Controller中的方法无法返回jsp页面；
2. 如果需要返回指定页面，则需用@Controller配合视图解析器InternalResourceViewResolver才行；
3. 如果需要返回JSON、XML或自定义Type到页面，则需在对应的方法上加@ResponseBody注解。


#### @GetMapping 
> @GetMapping是一个组合注解，是@RequestMapping(method = RequestMethod.GET)的缩写<br>

自Spring4.3引入{@GetMapping、PostMapping、PutMapping、DeleteMapping}简化常用的HTTP映射

#### @AutoWired 

---

## maven
每个工程只有一个POM文件  
1. 每个POM文件需要project元素和三个必须的字段：GroupId，ArtifactId和version
2. POM.xml的根元素是project，他有三个主要的子节点
 ```xml
<group>工程组的标识</group>
<artifactId>工程的标识</artifactId>
<version>工程的版本号</version>
```
---

## swagger API
  
常用的三种注解  
1）@Api用于类  
> 参数说明：url{路径信息}  

2）@ApiOperation用于方法  
> 参数说明：value{接口说明},httpMethod{接口请求方式}，response{接口返回参数类型}，note{接口发布说明}   

3）@ApiParam用于方法、参数、字段说明  
> 参数说明：required{是否必须参数},name{参数名称},value{参数参数具体描述}
  	
描述 | 名称 | 作用
:---:|:----|:---:
协议集描述|@Api|用于类上，说明类的作用
协议描述|@ApiOperation|用在方法上，说明方法的作用
响应方式|@ApiResponse|用在方法上，描述一个操作可能的返回结果(状态码及其描述)
响应方式集|@ApiResponses|为@ApiResponse集合，响应集配置
非对象参数描述|@ApiImplicitParam|用在方法上,需要使用Servlet或non-JAX-RS环境时定义参数
非对象参数集|@ApiImplicitParams|为@ApiImplicitParam集合
返回对象的意义|@ApiModel|用在返回对象类上，用于描述model信息
对象属性|@ApiModelProperty|用于方法、字段上，描述model属性或者数据操作更改

附：[swagger annotation docs](http://docs.swagger.io/swagger-core/v1.3.12/apidocs/index.html?com/wordnik/swagger/annotations)

---

## Spring Security

+ 认证(Authentication)：确认用户可以访问系统  
+ 授权(Authorization)：确认用户在当前系统中是否能够执行某个操作，即用户所拥有的功能权限

---