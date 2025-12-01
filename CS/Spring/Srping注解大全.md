#### @Async

异步执行该方法，会在默认的线程池中去执行。需要注意的是，一定要在外部的类中去调用这个方法，如果在本类调用则不起作用，比如this.saveLog()

@Async

public void saveLog() {

    System.err.println(Thread.currentThread().getName());

}

#### @Autowired

是spring提供的注解，默认**按类型装配**。它要求依赖对象必须存在，如果允许null值，可以设置它required属性为false。如果有多个bean都能满足依赖关系的话，Spring将会抛出一个异常。可以加到属性、构造方法、设值方法上。

@Autowired提供这样的规则，首先它会根据类型找到对应的Bean，如果对应类型的Bean不是唯一的，那么它会根据其属性名称和Bean的名称进行匹配。如果匹配得上，就会使用该Bean；如果还无法匹配，就会抛出异常。

#### @ActiveProfile

在测试时候使用特定的环境。

@RunWith(SpringJUnit4ClassRunner.class) @ContextConfiguration(classes=ProfileConfig.class)

@ActiveProfiles("upper")

public class Test4_UsingProfile {

    @Autowired

    Action action;

    @Test public void test3() {

        action.execute("wushuohanaaaaaa");

    }

}

#### @Bean

@Bean是一个方法级别上的注解，主要用在@Configuration注解的类里，也可以用在@Component注解的类里。默认的bean的id为方法名，也可以`@Bean(name="abc")`；

#### @Component("abc")

标注在类上，把普通pojo实例化到spring容器中，并设置其id，如果省略参数，则bean的id为其类名的首字母小写。相当于配置文件中的`<bean id="" class=""/>`

#### @ComponentScan

启用组件扫描，自动扫描指定包以及子包下所有使用**@Service**,**@Component**,**@Controller**,**@Repository**的类并注册。默认会扫描与配置类相同的包及子包。  
扫描其他包：

@ComponentScan("soundsystem")

@ComponentScan(basepackages="soundsystem")

@ComponentScan(basepackages={"soundsystem", "video"})

@ComponentScan(basepackageClasses={CDPlayer.class, DVDPlayer.class})//扫描该类包中的类和接口

相当于XML配置`<context:component-scan base-package="..." />`

#### @Conditional

标注在带有@Bean注解的方法上，如果给定的条件计算结果为true，就会创建这个bean，否则的话，这个bean会被忽略。

@Bean

@Conditional(MagicExistsCondition.class)

public MagicBean magicBean() {

    return new MagicBean();

}

注解的参数是一个实现Condition接口的类。  
@Profile注解也是由@Conditional注解实现。

#### @ConditionalOnProperty

Spring Boot通过**@ConditionalOnProperty**来控制**Configuration**是否生效

@Configuration

@ConditionalOnProperty(prefix="mf",name = "assert", havingValue = "true")

public class AssertConfig {

    @Autowired

    private HelloServiceProperties helloServiceProperties;

    @Bean

    public HelloService helloService(){

        HelloService helloService = new HelloService();

        helloService.setMsg(helloServiceProperties.getMsg());

        return helloService;

    }

}

//在application.properties配置"mf.assert"，对应的值为true时，该配置生效

#### @Configuration

注解表明这个类是一个配置类，该类应该包含在Spring应用上下文中如何创建bean的细节。**@Configuration被@Component注解修饰**

#### @ConfigurationProperties

将属性文件与一个Java类绑定，将属性文件中的变量值注入到该Java类的成员变量中。

myapp.mail.enable=true

myapp.mail.default-subject=This is a Test

@Data

@PropertySource("classpath:/config.properties")

@ConfigurationProperties("myapp.mail")

public class MailModuleProperties {

    private Boolean enable = Boolean.TRUE;

    private String defaultSubject;

}

详见**@ConfigurationProperties 注解使用姿势，这一篇就够了**

#### @Controller

这个注解是用来声明控制器的，它基于@Component注解。

#### @ControllerAdvice

spring3.2引入。控制器通知（controller advice）是任意带有@ControllerAdvice注解的类，这个类会包含一个或多个如下类型的方法：

- @ExceptionHandler注解标注的方法；
- @InitBinder注解标注的方法；
- @ModelAttribute注解标注的方法。

在带有@ControllerAdvice注解的类中，以上所述的这些方法会运用到整个应用程序所有控制器中带有@RequestMapping注解的方法上。  
@ControllerAdvice注解本身已经使用了@Component，因此@ControllerAdvice注解所标注的类将会自动被组件扫描获取到，就像带有@Component注解的类一样。

@ControllerAdvice

public class AppWideExceptionHandler {

    @ExceptionHandler(DuplicateSpittleException.class)

    public String duplicateSpittleHandler() {

        return "error/duplicate";

    }

}

所有控制器有@RequestMapping注解的方法跑出的DuplicateSpittleException异常都会由这个类来处理。

#### @EnableAsync

一般添加在启动类上开启异步任务的执行

#### @EnableConfigurationProperties

激活一个带有@ConfigurationProperties注解的类。  
详见**@ConfigurationProperties 注解使用姿势，这一篇就够了**

#### @Import

可以普通类导入到 IoC容器中，注册为Bean。只能注解在类上。一般用于导入配置类

@Configuration

@Import({CDPlayerConfig.class, CDConfig.class})

public class SoundSystemConfig {

}

#### @ImportResource

用来导入配置文件：

@Configuration

@Import(CDPlayerConfig.class)

@ImportResource("classpath:cd-config.xml")

public class soundSystemConfig {

}

#### @PathVariable

在控制器方法中绑定路径参数传递的数据到方法参数中。

@RequestMapping(value="/{spittleId}", method=RequestMethod.GET)

public String spittle(@PathVariable("spittleId") String spittleId, Model model) {

    model.addAttribute(spittleRepository.findOne(spittleId));

    return "spittle";

}

//若方法的参数名碰巧与占位符的名称相同，则可以省略@PathVariable的参数

@RequestMapping(value="/{spittleId}", method=RequestMethod.GET)

public String spittle(@PathVariable("spittleId") String spittleId, Model model) {

    model.addAttribute(spittleRepository.findOne(spittleId));

    return "spittle";

}

#### @Primary

在自动装配时，设置一个首选bean以避免歧义。  
可以装配在@Component注解的类或者@Bean注解的方法上。  
XML配置：`<bean id="" class="" primary="true" />`

#### @Profile

标注在类上（3.1~）、方法上（3.2~），是一个用来标明当前运行环境的注解。

/**

该配置类中的Bean，只有在dev环境下才会被初始化并注入容器。

*/

@Profile("dev")

@Configuration

public class A {

    @Bean

    ...

}

#### @PropertySource

注解在类上。  
@PropertySouce是spring3.1开始引入的基于java config的注解，作用是**加载属性文件**。  
通过@PropertySource注解将properties配置文件中的值存储到Spring的Environment中，Environment接口提供方法去读取配置文件中的值，参数是properties文件中定义的key值。

详见**spring properties文件.md**

#### @PropertySources

加载多个属性文件

@PropertySources({

    @PropertySource("classpath:config.properties"),

    @PropertySource("classpath:db.properties")})

public class AppConfig {

    //something

}

#### @Qualifier

它可以与@Autowired和@Inject协同使用，在注入的时候指定想要注入进去的Bean的限定符。也可以与@Component、@Bean一起使用，为Bean指定一个限定符。

@Qualifier("iceCream")

参数是Bean的id。更准确地  
讲，@Qualifier("iceCream")所引用的bean要具有String类型  
的“iceCream”作为限定符。如果没有指定其他的限定符的话，所有的  
bean都会给定一个默认的限定符，这个限定符与bean的ID相同。

#### @RequestBody

@RequestBody也能告诉Spring查找一个消息转换器，将来自客户端的资源表述转换为对象。因为Spittle参数上使用了@RequestBody，所以Spring将会查看请求中的Content-Type头部信息，并查找能够将请求体转换为Spittle的消息转换器。

@RequestMapping(method=RequestMethod.POST, consumes="application/json")

public @ResponseBody Spittle saveSpittle(@RequestBody Spittle spittle) {

    return spittleRepository.save(spittle);

}

#### @RequestMapping

可以在控制器类的级别或其中的方法的级别上使用。在方法上使用的注解是对在类上使用注解的补充。  
处理多个参数：  
`@RequestMapping(value={"/", "homepage"}, method=RequestMethod.GET, produces="application/json")`  
`@RequestMapping(value={"/", "homepage"}, method=RequestMethod.POST, consumes="application/json")`  
value:请求的url  
method:匹配请求的方法  
produces:匹配请求头中Accept域为"application/json"的请求，其他类型的不匹配。  
consumes:匹配请求头中Content-Type域为"application/json"的请求，其他类型的不匹配。

#### @RequestParam

把url中的请求参数绑定到方法的参数上。

@RequestMapping(method=RequestMethod.GET)

public List<Spittle> spittles(@RequestParam(value="max", defaultValue="2147183647") long max,

                              @RequestParam(value="count", defaultValue="20") int count) {

    return spittleRepository.findSpittles(max, count);

}

#### @RequestPart

注释在控制器方法的参数前，把上传的文件与MultipartFile绑定。

@RequestMapping(value="/register", method=POST)

public String processRegistration(@RequestPart("profilePicture") MultipartFile profilePicture,

                                  @Valid Spitter spitter, Errors errors) {//Errors参数要紧跟在带有@Valid标解的参数后面

    if (errors.hasErrors())

        return "registerForm";

    spitterRepository.save(spitter);

    try {

        profilePicture.transferTo(new File("C:\\test\\files\\" + profilePicture.getOriginalFilename()));

    } catch (IOException e) {

        e.printStackTrace();

    }

    return "redirect:/spitter/" + spitter.getUsername();

}

#### @Resource

是JDK1.6提供的注解。@Resource注解和@Autowired一样，也可以标注在字段或方法上，但它默认按名称装配。名称可以通过@Resource的name属性指定，如果没有指定name属性，当注解标注在字段上，即默认取字段的名称作为bean名称寻找依赖对象，当注解标注在属性的setter方法上，即默认取属性名作为bean名称寻找依赖对象。

#### @ResponseBoby

@ResponseBody注解会告知Spring，我们要将返回的对象作为资源发送给客户端，并将其转换为客户端可接受的表述形式。更具体地讲，DispatcherServlet将会考虑到请求中Accept头部信息，并查找能够为客户端提供所需表述形式的消息转换器。

@RequestMapping(method=RequestMethod.GET, produces="application/json")

public @ResponseBody List<Spittle> spittles(

        @RequestParam(value="max", defaultValue=MAX_LONG_AS_STRING) long max,

        @RequestParam(value="count", defaultValue="20") int count) {

    return spittleRepository.findSpittles(max, count);

}

#### @ResponseStatus

可以设置返回响应的状态码。

@ExceptionHandler(SpittleNotFoundException)

@ResponseStatus(HttpStatus.NOT_FOUND)    //设置响应的状态码为404

public @ResponseBody Error spittleNotFound(SpittleNotFoundException e) {

    long spittleId = e.getSpittleId();

    return new Error(...);

}

#### @RestController

使用@RestController来代替@Controller的话，Spring将会为该控制器的所有处理方法应用消息转换功能。  
相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了，但使用@RestController这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面。

#### @RunWith

JUnit用例都是在Runner(运行器)来执行的。通过它,可以为这个测试类指定一个特定的Runner。那么大多数时候我们都没有使用@RunWith这个注解,这是为什么呢?其实,JUnit中有一个默认的Runner,它的名字叫BlockJunit4ClassRunner,但这是在JUnit4.4之后才引入的,对于4.4之前版本的JUnit,它的名字叫Junit4ClassRunner。

@RunWith(SpringJUnit4ClassRunner.class) //使用junit4进行测试

@ContextConfiguration(locations={"classpath:applicationContext.xml"}) //加载配置文件

public Testclass {

    ...

}

#### @Scope

声明Bean的作用域，与@Component与@Bean一起使用。  
Spring定义了4中作用域：

- Singleton：单例
- Prototype：每次注入或获取Bean，都会创造新的实例。
- Session：在Web应用中，为每个会话创建一个Bean实例。
- Request：在Web应用中，为每个请求创建一个Bean实例。

@Component

@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)

//@Scope(value=WebApplicationContext.SCOPE_SESSION, proxyMode=ScopedProxyMode.INTERFACES)

//@Scope(value=WebApplicationContext.SCOPE_SESSION, proxyMode=ScopedProxyMode.TARGET_CLASS)

@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)

public class Notepad {

}

如果存在需要把Session或者Request作用域的Bean注入到Singleton作用域的Bean中，则需要使用代理，代理需要注入的Bean。如果需要注入的Bean实现了接口，则可以使用基于接口的代理，否则就需要使用CGLib来生成基于类的代理。

![](http://wiznote-desktop/ks/note/view/56e50298-30d1-4f11-ac7b-ff41b6d536f8/3bdeee7a-1776-4ee8-8337-78c4745a1900/index_files/8VQVD@EFN_24XU1X_7B1LQ4M8WW1547282184602.png)

#### @Value

注入值：

@Value("张三")

private String userName;

@Value("26")

private Integer age

注入外部的值：

1. 使用`@Value("${...}")`方式：  
    　　`${...}`是占位符，可以获取属性文件中对应的值（如果属性文件中没有这个属性，则会报错。可以通过赋予默认值解决这个问题，如`@Value("${attr:127.0.0.1}")`。
2. 使用@Value("#{...}")方式：  
    　　#{…}的{}里面的内容必须符合SpEL表达式,可以是文字表达式、Bean属性和方法、类表达式、访问properties(如：systemProperties 和 systemEnvironment)，具体参考SpEL。
3. 在@Value（...）中 `${…}`和#{…}混合使用：  
    　　注意必须#{}外面，`${}`在里面，因为spring执行${}是时机要早于#{}。

@Component

public class Test {

    @Value("#{systemProperties['user.language']}")

    private String language;

    @Value("${spelDefault.value:127.0.0.1}")

    private String spelDefault;

    @Value("#{'Hello World!.bytes.length'}")

    private String helloWorldBytes;

    @Value("#{'${server.name}'.split(',')}")

    private List<String> servers;

}

## Spring MVC

### 校验表单

#### @AssertFalse

所注解的元素必须是Boolean类型，并且值为false

#### @AssertTrue

所注解的元素必须是Boolean类型，并且值为true

#### @DecimalMax

所注解的元素必须是数字，并且它的值要小于或等于给定的BigDecimalString值

#### @DecimalMin

所注解的元素必须是数字，并且它的值要大于或等于给定的BigDecimalString值

#### @Digits

所注解的元素必须是数字，并且它的值必须有指定的位数

#### @Future

所注解的元素的值必须是一个将来的日期

#### @Max

所注解的元素必须是数字，并且它的值要小于或等于给定的值

#### @Min

所注解的元素必须是数字，并且它的值要大于或等于给定的值

#### @NotNull

所注解元素的值必须不能为null

#### @Null

所注解元素的值必须为null

#### @Past

所注解的元素的值必须是一个已过去的日期

#### @Pattern

所注解的元素的值必须匹配给定的正则表达式

#### @Size

所注解的元素的值必须是String、集合或数组，并且它的长度要符合给定的范围

#### @Valid

@Valid在控制器方法中注解所标注要检验的参数。要校验的参数后面紧跟Errors对象，此对象包含错误信息。

@RequestMapping(value="/register", method=POST)

public String processRegistration(@Valid Spitter spitter, Errors errors) {//Errors参数要紧跟在带有@Valid标解的参数后面

    if (errors.hasErrors())

        return "registerForm";

    spitterRepository.save(spitter);

    return "redirect:/spitter/" + spitter.getUsername();

}

### 异常

#### @ExceptionHandler

在Controller中编写异常处理方法，使用该注解标记处理方法，它能够处理Controller中所有带有@RequestMapping的注解的方法所抛出的DuplicateSpittleException异常，并将其渲染成视图。

@ExceptionHandler(DuplicateSpittleException.class)

public String handleDuplicateSpittle() {

    return "error/duplicate";

}

还可以配合@ControllerAdvise使用，处理所有controller中抛出的异常

@ControllerAdvice

public class DefaultExceptionHandler {

    @ExceptionHandler({UnauthorizedException.class})

    @ResponseStatus(HttpStatus.UNAUTHORIZED)

    public ModelAndView processUnauthenticatedException(NativeWebRequest request, UnauthorizedException e) {

        ModelAndView mv = new ModelAndView();

        mv.addObject("ex", e);

        mv.setViewName("unauthorized");

        return mv;

    }

}

#### @ResponseStatus

将异常映射为特定的HTTP状态码

@ResponseStatus(value=HttpStatus.NOT_FOUND, reason="Spittle Not Found")

public class SpittleNotFoundException extends RuntimeException {

}

在引入@ResponseStatus注解之后，如果控制器方法抛出SpittleNotFound-Exception异常的话，响应将会具有404状态码，这是因为Spittle Not Found。

### AOP

#### @Aspect

注解在类上，声明切面

#### @After

通知方法会在目标方法返回或抛出异常后调用。

#### @AfterReturning

通知方法会在目标方法返回后调用

#### @AfterThrowing

通知方法会在目标方法抛出异常后调用

#### @Around

通知方法会将目标方法封装起来

#### @Before

通知方法会在目标方法调用之前执行

#### @Pointcut

在一个切面中定义可重用的切点

/*

切面类需要在配置中声明为Bean，还要启用自动代理功能。

*/

@Aspect

public class Audience {

    @Pointcut("excution(** concert.Performance.perform(..))")

    public void performance() {}

    @Pointcut("excution(* soundsystem.compactDisc.playTrack(int) && args(trackNumber))")        //需要传递参数的切点

    public void trackPlayed(int trackNumber) {}

    @Before("trackPlayed(trackNumber)")

    public void countTrack(int trackNumber) {

        ...

    }

    @Before("performance()")

    public void silenceCellPhones() {

        ...

    }

    @Before("performance()")

    public void takeSeats() {

        ...

    }

    @AfterReturning("performance()")

    public void applause() {

        ...

    }

    @AfterThrowing("performance()")

    public void demandRefund() {

        ...

    }

    @Around("performance()")        //环绕通知

    public void watchPerformance(ProceedingJoinPoint jp) {

        try {

            ...

            jp.proceed();

            ...

        } catch (Throwable e) {

            ...

        }

    }

}

#### @EnableAspectJAutoProxy

在配置类上通过使用该注解启用自动代理功能。同XML配置`<aop:aspectj-autoproxy>`  
AspectJ自动代理都会为使用@Aspect注解的bean创建一个代理，这个代理会围绕着所有该切面的切点所匹配的bean。

## Spring Security

#### @EnableWebSecurity

将会启用Web安全功能。

@Configuration

@EnableWebSecurity  //启用Web安全性

public class SecurityConfig extends WebSecurityConfigurerAdapter {

}

#### @EnableGlobalMethodSecurity

启用基于注解的方法安全性

@Configuration

@EnableGlobalMethodSecurity(securedEnabled=true,prePostEnabled=true)    //如果securedEnabled属性的值为true的话，将会创建一个切点，这样的话Spring Security切面就会包装带有@Secured注解的方法。prePostEnabled启用表达式实现方法级别的安全性。

public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {

    //这个类能够为方法级别的安全性提供更精细的配置。

}

#### @Secured

基于用户所授予的权限限制对方法的访问。

@Secured({"ROLE_SPITTER", "ROLE_ADMIN"})

public void addSpittle(Spittle spittle) {

}

#### @PreAuthorize

在方法调用之前，基于表达式的计算结果来限制对方法的访问

@PreAuthorize("(hasRole('ROLE_SPITTER') and #spittle.test.length() <= 140) or hasRole('ROLE_PREMIUM')")

public void addSpittle(Spittle spittle) {

    //一般用户只能写140个字以内的Spittle，而付费用户不限制字数。

}

#### @PostAuthorize

允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常

@PostAuthorize("returnObject.spitter.username == principal.username")

public Spittle getSpittleById(long id) {

    //Spring Security在SpEL中提供了名为returnObject的变量。在这里，我们知道返回对象是一个Spittle对象，所以这个表达式可以直接访问其spittle属性中的username属性。在对比表达式双等号的另一侧，表达式到内置的principal对象中取出其username属性。principal是另一个Spring Security内置的特殊名称，它代表了当前认证用户的主要信息（通常是用户名）。在Spittle对象所包含Spitter中，如果username属性与principal的username属性相同，这个Spittle将返回给调用者。否则，会抛出一个AccessDeniedException异常，而调用者也不会得到Spittle对象。

}

#### @PostFilter

允许方法调用，但必须按照表达式来过滤方法的结果

@PreAuthorize("hasAnyRole({'ROLE_SPITTER', 'ROLW_ADMIN'})")

@PostFilter("hasRole('ROLW_ADMIN') || filterObject.spitter.username == principle.name")

public List<Spittle> getOffensiveSpittles() {

    //filterObject对象引用的是这个方法所返回List中的某一个元素

}

#### @PreFilter

允许方法调用，但必须在进入方法之前过滤输入值

@PreAuthorize("hasAnyRole({'ROLE_SPITTER', 'ROLW_ADMIN'})")

@PreFilter("hasRole('ROLW_ADMIN') || targetObject.spitter.username == principle.name")

public void deleteSpittles(List<Spittle> spittles) {

    //targetObject是Spring Security提供的另外一个值，它代表了要进行计算的当前列表元素。

}

## Spring Cloud

#### @EnalbeDiscoveryClient

表示当前服务是一个Eureka的客户端

#### @EnableEurekaServer

表示当前服务是一个Eureka的服务器