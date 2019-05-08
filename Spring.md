## Spring

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


### 元注解
1. @Document，说明该注解被包含在javadoc中；
2. @Target，定义注解的作用目标，包括类、字段和方法等等  
`@Target(ElementType.TYPE)`//接口、类、枚举、注解  
`@Target(ElementType.FIELD)`//字段、枚举的常量  
`@Target(ElementType.METHOD)`//方法  
`@Target(ElementType.PARAMETER)`//方法参数  
`@Target(ElementType.CONSTRUCTOR)`//构造函数  
`@Target(ElementType.LOCAL_VARIABLE)`//局部变量  
`@Target(ElementType.ANNOTATION_TYPE)`//注解  
`@Target(ElementType.PACKAGE)`//包
3. @Retention，定义注解的保留策略  
`@Retention(RetentionPolicy.SOURCE)`//注解仅存在于源码中，在class字节码文件中不包含  
`@Retention(RetentionPolicy.CLASS)`// 默认的保留策略，注解会在class字节码文件中存在，但运行时无法获得  
`@Retention(RetentionPolicy.RUNTIME)`// 注解会在class字节码文件中存在，在运行时可以通过反射获取到    
4. @Inherited, 说明子类可以继承父类中的该注解  


### 注解@Component、@Repository、@Service、@Controller区别

+ @Controller---控制层组件
+ @Service------业务层组件
+ @Repository---持久层组件，用于标记数据访问，即DAO组件
+ @Component----泛指组件，当组件不好归类时，可以使用这个注解进行标注  

如果 Web 应用程序采用了经典的三层分层结构的话，最好在持久层、业务层和控制层分别采用 @Repository、@Service 和 @Controller 对分层中的类进行注释，而用 @Component 对那些比较中立的类进行注释

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

### Validation验证机制

#### @Valid和@Validated
+ @Validated: 可用在类型、方法和方法参数上，但是不能用在成员属性（字段）上
+ @Valid: 可用在方法、构造函数、方法参数和成员属性（字段）上  

嵌套验证：@Validated和@Valid加在方法参数前，都不会自动对参数进行嵌套验证  
##### 两者区别：
+ @Validated：用在方法入参上无法单独提供嵌套验证功能。不能用在成员属性（字段）上，也无法提示框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。
- @Valid：用在方法入参上无法单独提供嵌套验证功能。能够用在成员属性（字段）上，提示验证框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。

### @NotNull和@NotEmpty和@NotBlank区别  
+ @NotNull 不能为null，但可以是empty
  > `("", " ", "  ")`
- @NotEmpty 不能为null，而且长度必须大于0
  > `(" ", "  ")`
+ @NotBlank 只能作用在String上，不能为null，而且调用trim()后长度须大于0
  > `("test")`

---

## SpringBoot

### CLI(Spring Boot Commad Line)
> 是一个命令行工具，可以用它来快速构建Spring原型应用。
  
windows下安装cmd命令  
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
> 可以对类成员变量、方法及构造函数进行标注，完成自动分配  


#### @GeneratedValue
> 在JPA中，@GeneratedValue注解是为了一个实体生成一个唯一标识的主键

有四种主键生成策略，被定义在枚举类GenerationType中<br/>
1. `@Id @GeneratedValue(strategy=GenerationType.AUTO) long id`  
这种方式会把主键生成策略交给持久化引擎，持久化引擎会选择下面其中一种，
由于JPA默认是AUTO方式，所以不用特意指定策略，例：`@Id @GeneratedValue long id`
2. `@Id @GeneratedValue(strategy=GenerationType.IDENTITY) long id`  
即数据库中的主键自增长，包括MySQL在内的大部分数据库支持  
3. `@Id @GeneratedValue(strategy=GenerationType.SEQUENCE) long id`  
部分数据库(Oracle, PostgreSQL, DB2等)生成“序列(sequence)”对象，与注解@SequenceGenerator一起用于主键生成策略  
4. `@Id @GeneratedValue(strategy=GenerationType.TABLE) long id`  
使用一个特定的数据表格来保存主键，不依赖与外部环境和数据库具体实现，容易移植。
一般与另一个注解@TableGenerator搭配使用  

更多详解请参略:[Auto Generated Values](https://www.objectdb.com/java/jpa/entity/generated)

#### @Transient

+ hibernate JPA注解，该属性不与数据库字段做映射，ORM框架将忽略该属性  
- 若实现Serializable接口，该属性也不会被序列化  

---

## maven  

### POM文件  
1. 每个POM文件需要project元素和三个必须的字段：GroupId，ArtifactId和version
2. POM.xml的根元素是project，他有三个主要的子节点
 ```xml
 <parent>
    <group>工程组的标识</group>
    <artifactId>工程的标识</artifactId>
    <version>工程的版本号</version>
</parent>

```

### mvn常见命令

<br>

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

## Spring Data JPA

### JpaRepository接口
```java
public interface JpaRepository<T,ID> extends PagingAndSortingRepository<T,ID>, QueryByExampleExecutor<T> {}
```
> JpaRepository继承了接口PagingAndSortingRepository和QueryByExampleExecutor；
PagingAndSortingRepository又继承CrudRepository，因此，JpaRepository接口同时拥有了基本CRUD功能以及分页功能。

Repository继承两个接口
```java
public interface EntityRepository<TKey extends Serializable, TEntity extends Entity<TKey>>
        extends JpaRepository<TEntity, TKey>, JpaSpecificationExecutor<TEntity> {
}
```
若查询是不带查询条件(例只做列表展示和分页)的，则只需继承JpaRepository接口即可；若查询带查询条件则需要继承JpaSpecificationExecutor接口。

### @Temporal
+ @Temporal(TemporalType.DATE)实体类会封装成日期“yyyy-MM-dd”的Date类型
+ @Temporal(TemporalType.TIME)实体类会封装成时间“hh-MM-ss”的Date类型
+ @Temporal(TemporalType.TIMESTAMP)实体类会封装成完整的时间“yyyy-MM-dd hh:MM:ss”的Date类型