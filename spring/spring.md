# Spring


***
### RequestParam vs QueryParam:

RequestParam is in Spring and QueryParam is in JAX-RS.

### PUT vs POST:

> both can be used creating. but post is used to create a List of entries while put is used for a single entry.
> put is idempotent. means if you yse put twice the result is same.
> post to create and put to update.


### PUT vs PATCH

> put is idempotant, means in order to update any resource we have to send the whole payload to avoid overriding the existing property in DB to null.
whereas in PATCH we have to send only the property that needs to be updated. Hence uses less bandwidth.

> Example:
    lets say there is a house and we need to update the no. of windows. for put we have to do:

        {
            "address" : "plot-1",
            "door": 4,
            "windows": 5,
            "rooms": 3"
        }
    
here we have to send the whole data, if we do not send they will be treated as null and override the DB entry.
here's for PATCH:

        {
            "windows": 5
        }

as PATCH is not idempotent hence it doesn't require to produce the same result.
if the resource is not available it will simply not create it.

***

* Spring beans are Java objects that are managed by the Spring container.
* The Spring container is responsible for instantiating, configuring, and assembling the Spring beans.
* Spring can create bean even if the class constructor is private.

* DI is a form of IoC, where implementations are passed into an object through constructors/setters/service lookups, which the object will 'depend' on in order to behave correctly.
`IoC without using DI`, for example would be the Template pattern because the implementation can only be changed through sub-classing.

* *destroy method is not called for beans of scope prototype.* As Spring context doesn't keep track of the prototype scope. As it will cause memory leak, as spring doen't know when to dispose it.

* In WebApplication, we dont need to close the Context as it will close at server shutdown.
* `@Autowired` is default to wire by Type.
* `@Resource` is wired by name. and it only supports setters and fields whereas *@Autowired supports fields, Setters, constructors and multi-argument methods.*

* We have three types of Dependency injection
    1)  Constructor Injection
    2)  Setter/Getter Injection
    3)  Interface Injection  

  Spring will support only Constructor Injection and Setter/Getter Injection.

## @SpringBootApplication consists of:

@Configuration to enable Java-based configuration  
@ComponentScan to enable component scanning.  
@EnableAutoConfiguration to enable Spring Boot's auto-coThenfiguration feature.  

### Spring bean factory vs Spring ApplicationContext:
* both are interfaces.
* ApplicationContext extends BeanFactory.
* BeanFactory doesn't provide support for internationalization i.e. i18n
* *Bean factory implementation is `BeanFactory bean = new XMLBeanFactory(new FileSystemResource(Spring.xml));`
* *ApplicationContext implementation is `ApplicationContext context = new ClassPathXmlApplicationContext("spring1.xml");`
* In BeanFactory, the implementations use lazy loading, which means that beans are only instantiating when we directly calling them through the getBean() method.
* ApplicationContext It uses eager loading, so every bean instantiate after the ApplicationContext is started up.

**By default, Spring creates all singleton beans eagerly at the startup/bootstrapping of the application context**

            	
    @Lazy
    @Component
    public class City {
        public City() {
            System.out.println("City bean initialized");
        }
    }
    And it's reference:

    public class Region {
    
        @Lazy
        @Autowired
        private City city;
    
        public Region() {
            System.out.println("Region bean initialized");
        }
    
        public City getCityInstance() {
            return city;
        }
    }

*Here the City bean will only get initialized when `getCityInstance()`is called.*

* `@primary:` If a bean has @Autowired without any @Qualifier, and multiple beans of the type exist, the candidate bean marked @Primary will be chosen, i.e. *it is the default selection* when no other information is available, i.e. *when @Qualifier is missing*.

* @Qualifier > @Primary
* @Qualifier should be used in conjuction with @Autowired always.
* @Primary should be used in conjuction with @Bean / @Autowired


***
## Reading external Config in Spring:

1. using propertySourceplaceholder bean.
2. As command Line Argument.
        java -jar app.jar --spring.config.location=file:///Users/home/config/jdbc.properties
3. @PropertySource
4. System.getProperties() //Java system properties.
5. Profile Specific inside and outside the jar.
6. in commandline provide a json:
        java -jar myapp.jar --spring.application.json='{"name":"test"}'
    
### @ConfigurationProperties and Property Source
* lets say you have yml as below:

        spring:
          datasource:
            url...

To Read the properties and mapping it to a class we can do:

    @PropertySource("classpath:configprop.yml)
    @ConfigurationProperties(prefix="datasource")
    public class Datasource{
        private String url....
    }

and if we dont use `@Configuration` on the POJO we have to use `@EnableConfigurationProperties` on SpringbootApplication.
***
Internally the DispatchServlet will look for `@Controller` annotated classes and not `@Component` annotated class to look for `@RequestMapping`
Similarly `@Repository` and `@Service` annotation are specialized version of `@Component`.
A class annotated with `@Component` is eligible for auto-detectection on classpath scanning.

***
#### @Configuration

* it indicates that a class declares one or more bean methods. (`@Bean`)
* bootstraping these classes are done using `@AnnotationConfigApplicationContext`
* for adding bean from xml we need `<context: annotation-config/>`
* `@Configuration` classes are annotated with `@Component` hence eligible for comopnent scanning.
***

#### @Bean
* Indicates that a method produces a bean to be managed by Spring Container.
* we can pass multiple values in `@bean({"b1", "b2"})`
*


***

@Bean vs @Component
* Component is a class level Annotation, whereas bean is a method level.
* @bean decouples the declaration by Spring auto-scanning, and lets you give more command on how to create a bean.

***
@Profile 

Initializes bean only if specific Spring profile is active dev/QA etc.
***

@Scope

Defines the scope of a bean, by default they are all singleton.
***

@DependsOn

Enforces the creation of specific another bean before creation of this bean.

static @Bean Method
by marking a method as static, it can be invoked without
***

## Transactional

* for `Transactional` you have to mark the Application with `@EnableTransactionManagement` and use `@Transactional` on either Method level or Class Level.
* `Transactional` take 3 parameters: 
    1. Propagation: it would be either `required` or `required_new`. As if you want to use any existing transaction(creates if not present)  
    or `required_new` will always create a new transaction, Suspends any existing one.

    2. Isolation: defines the data contract for reads,
        a. read_uncommitted.
        b. read_committed.
        c. repeatable read: if a row is read twice the record would be same.
        d. serializable: perfoms all the transactions in sequence.

    3. rolloutFor:
        takes in an expection for which the transaction should be rolledback for. Generally takes in `throwable` but can take any specific exception.

***

## @Async

* In Order to acheieve Async operations in Spring we use @Aysnc annotation.
* To implement `Async` we use `@EnableAsync` on SpringbootApplication.
* Create a `ThreadPoolExecuter` configuration class where we define the Thread pool capacity , Min, Max, name and other parameters.
* When we run an `Async` method it always has to be in a new instance of a class, i.e a new Class. Calling Aysnc method from same class would not create Async threads.
* Annotate all the methods with @`@Aysnc`. It can be either used in class/interface.
* for a `void` return type we do not have to care much but for any method with Return type, we can only return `CompletableFuture` type only.
    Hence the syntax becomes: `public CompletableFuture<String> helloWorld(){}`
* CompletableFuture ia not a Serialized Object. Will throw error if we try to do so.
* we can use: `<CompletableFuture>` `obj.get()` to retrieve the actual object from the future return type.

***
## Dynamically Determing the  Datasource
for this we use `abstractRoutingDataSource` interface.

***
## Spring Boot Thin Launcher

 Spring Boot Thin Launcher. It creates a jar file with your application code but none of its dependencies. It adds a special thin launcher that 
 knows how to resolve your application's dependences
 from a remote Maven repository or from a local cache when the jar is executed. Judging by the description of what you want to do,
 you'd utilise the local cache option.
 
         <build>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <dependencies>
                        <dependency>
                            <groupId>org.springframework.boot.experimental</groupId>
                            <artifactId>spring-boot-thin-layout</artifactId>
                            <version>1.0.3.RELEASE</version>
                        </dependency>
                    </dependencies>
                    <configuration>
                        <executable>true</executable>
                    </configuration>
                </plugin>
            </plugins>
        </build>
