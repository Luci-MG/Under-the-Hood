# **Spring Core Framework**

the foundation of the entire Spring ecosystem. We'll explore each component and mechanism in detail, so by the end, you’ll have a thorough understanding of how Spring Core works, including the IoC container, Dependency Injection (DI), Beans, ApplicationContext, Bean Lifecycle, AOP, and more.


## **What is Spring Core Framework**

The Spring Core Framework is the heart of the Spring ecosystem. It provides the essential features required to build Java applications, with a focus on dependency injection (DI) and inversion of control (IoC). At its core, Spring aims to eliminate the complexities of creating objects, managing dependencies, and wiring different components together.


## **Modules**

### **Spring Core**
The foundational module that provides the IoC container and the basic tools for dependency injection (DI), It includes the core interfaces and classes like BeanFactory, ApplicationContext, BeanPostProcessor, BeanDefinition, and others.

### **Spring Beans**
Manages the configuration, creation, and lifecycle of Spring beans.
  
### **Spring Context**
Provides a runtime environment for applications using the IoC container. It builds on the Spring Core and adds additional functionality like events and internationalization (i18n).

### **Spring SpEL**
SpEL(Spring Expression Language) A powerful expression language that can be used to dynamically query or manipulate bean properties.


## **Core Concepts**

### **Inversion of Control (IoC)**

The Inversion of Control (IoC) principle is at the core of the Spring Framework. It shifts the control of object creation and management from the developer to the IoC container, promoting loose coupling and enhancing testability. Let’s break it down conceptually and then dive into the Spring implementation.

In traditional programming, the application code creates and manages its dependencies directly.

???+ example
    ```java
    public class OrderService {
        private PaymentService paymentService;

        public OrderService() {
            this.paymentService = new PaymentService(); // Tight coupling
        }
    }
    ```

    !!! info "Explanation"
        - The `OrderService` class directly instantiates `PaymentService`, meaning these two classes are tightly coupled. If the `PaymentService` class changes, you must modify the `OrderService` code as well.
        - This design also makes unit testing harder since the `OrderService` always uses a real `PaymentService` instead of a mock service.


**IoC Solution**
With Inversion of Control (IoC), the responsibility of creating the `PaymentService` is "inverted" and delegated to the Spring IoC container. Now, the IoC container injects the dependency into `OrderService`.

???+ example "IoC Solution Example"
    ```java
    @Component
    public class OrderService {
        private final PaymentService paymentService;

        @Autowired
        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;  // Dependency injection
        }
    }
    ```

    !!! info "Explanation"
        - The `OrderService` no longer creates the `PaymentService` itself.
        - The Spring IoC container handles the lifecycle of `PaymentService` and provides it to `OrderService`.

---

### **Types of IoC Containers**

#### **BeanFactory**
BeanFactory is the basic IoC container in Spring. It provides basic dependency injection and bean management functionality. 

Features of BeanFactory:

- Lazy Initialization: Beans are created only when they are requested.
- Minimal memory usage since beans are not pre-initialized.
- Useful in scenarios with resource constraints, such as mobile applications.

???+ example "Usage Example"
    ```java
    BeanFactory factory = new XmlBeanFactory(new FileSystemResource("beans.xml"));
    OrderService service = (OrderService) factory.getBean("orderService");
    ```

However, BeanFactory is rarely used now because it lacks advanced features like event propagation, internationalization, and eager initialization, which are provided by ApplicationContext.


#### **ApplicationContext**
ApplicationContext is a more powerful IoC container that extends BeanFactory. It is widely used in modern Spring applications because of its rich features.

Features of ApplicationContext:

- Eager Initialization: Beans are instantiated during startup, improving performance for large-scale applications.
- Event Propagation: Publishes and listens to application events (like `ContextRefreshedEvent`).
- Internationalization (i18n): Supports localization for different languages.
- Bean Autowiring: Automatically injects dependencies with @Autowired or other annotations.
- Environment Abstraction: Manages application profiles and environment variables.

???+ example "Usage Example"
    ```java
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    OrderService service = context.getBean(OrderService.class);
    ```

In most cases, developers use `AnnotationConfigApplicationContext` or `Spring Boot` to load configurations and manage beans.


#### **BeanFactory vs ApplicationContext**

| Aspect               | BeanFactory                      | ApplicationContext              |
|--------------------------|---------------------------------------|------------------------------------|
| Bean Initialization  | Lazy (on-demand)                     | Eager (at startup)                |
| Event Handling        | Not supported                        | Supports event handling           |
| Internationalization | Not supported                        | Supports i18n                     |
| Bean Lifecycle Hooks | Basic                                | Full support for lifecycle hooks |
| Common Usage         | Legacy or constrained environments   | Modern Spring applications        |


### **IoC Flow**

!!! info "Step-by-Step"
    1. Define Beans and Dependencies: Beans can be defined in XML, Java Configuration, or through Annotations like `@Component`.
    ```xml
    <bean id="paymentService" class="com.example.PaymentService"/>
    <bean id="orderService" class="com.example.OrderService">
        <constructor-arg ref="paymentService"/>
    </bean>
    ```

    2. Spring IoC Container Loads Configuration: The IoC container reads the configuration (XML, annotations, or Java-based) during startup.

    3. Dependency Injection (DI): The IoC container identifies the dependencies and injects them using constructor, setter, or field injection.

    4. Bean Initialization: The IoC container initializes all necessary beans (eagerly or lazily).

    5. Bean Usage: The beans are now available for use by the application.


### **Dependency Injection (DI)**

#### **What is DI ?**
Dependency Injection (DI) is a pattern where objects are provided with their dependencies at runtime by the IoC container instead of creating them directly. Spring supports multiple types of dependency injection:

#### **Types of DI**

**Constructor Injection:** Dependencies are provided through the class constructor, Recommended for mandatory dependencies.
???+ example "Constructor Injection Example"
    ```java
    @Component
    public class OrderService {
        private final PaymentService paymentService;

        @Autowired
        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    }
    ```

**Setter Injection:** Dependencies are injected using setter methods, Useful for optional dependencies.
???+ example "Setter Injection Example"
    ```java
    @Component
    public class OrderService {
        private PaymentService paymentService;

        @Autowired
        public void setPaymentService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    }
    ```

**Field Injection:** Dependencies are injected directly into class fields, Not recommended since it makes unit testing harder.
???+ example "Field Injection"
    ```java
    @Component
    public class OrderService {
        @Autowired
        private PaymentService paymentService;
    }
    ```


### **Why IoC and DI are Essential**

- Loose Coupling: Objects don’t manage their dependencies, making them easier to modify, extend, or replace.

- Testability: Dependencies can be easily mocked during unit tests, enhancing test coverage.

- Reusability: Components are easier to reuse across different parts of the application.

- Configuration Management: Centralized configuration of beans ensures that all components behave consistently.


### **Challenges with IoC**

- Configuration Overhead: Without frameworks like Spring, managing dependencies can lead to complex code. Spring solves this by centralizing configurations.

- Circular Dependencies: Two beans dependent on each other can cause issues. Spring detects such circular dependencies and throws exceptions.

- Managing Large Applications: With many components, configuration can get complicated. Spring Boot simplifies this with auto-configuration and starters.


### **Full IoC Implementation**

Let’s look at a complete example using Spring Core with constructor injection:

???+ example "Full IoC Implementation Example"
    ```java title="Java Configuration Example"
    @Configuration
    public class AppConfig {

        @Bean
        public PaymentService paymentService() {
            return new PaymentService();
        }

        @Bean
        public OrderService orderService(PaymentService paymentService) {
            return new OrderService(paymentService);
        }
    }
    ```

    ```java title=" OrderService and PaymentService"
    @Component
    public class PaymentService {
        public void processPayment() {
            System.out.println("Payment processed.");
        }
    }

    @Component
    public class OrderService {
        private final PaymentService paymentService;

        @Autowired
        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }

        public void placeOrder() {
            System.out.println("Order placed.");
            paymentService.processPayment();
        }
    }
    ```

    ```java title="Main Class to Run"
    public class Main {
        public static void main(String[] args) {
            ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
            OrderService orderService = context.getBean(OrderService.class);
            orderService.placeOrder();
        }
    }
    ```

## **Beans and IoC Container**

Spring beans are the building blocks of any Spring application. They represent the objects that the Spring IoC container manages throughout their lifecycle. Understanding beans and their lifecycle is critical for mastering the Spring Core framework. Let’s explore everything about beans—from creation, scopes, lifecycle, initialization, destruction, and more—in detail.

### **What is a Bean ?**
A bean in Spring is an object that is instantiated, assembled, and managed by the IoC container. The container creates, initializes, and wires these beans, ensuring that all dependencies are injected as needed. Beans are usually defined using:

- XML configuration files
- Java-based configuration classes
- Annotations (`@Component`, `@Service`, `@Repository`, `@Controller`)

### **Configuring Beans**

Spring provides multiple ways to declare beans and register them with the IoC container:

#### **XML-based Configuration**
Traditional way of defining beans using XML files.
???+ example "XML Config Example"
    ```xml
    <beans xmlns="http://www.springframework.org/schema/beans" 
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="paymentService" class="com.example.PaymentService"/>
        <bean id="orderService" class="com.example.OrderService">
            <constructor-arg ref="paymentService"/>
        </bean>
    </beans>
    ```

#### **Java-based Configuration**
Spring allows you to use Java classes to define beans. This is cleaner and avoids XML boilerplate.

???+ example "Java Config Example"
    ```java
    @Configuration
    public class AppConfig {

        @Bean
        public PaymentService paymentService() {
            return new PaymentService();
        }

        @Bean
        public OrderService orderService() {
            return new OrderService(paymentService());
        }
    }
    ```

#### **Component Scanning with Annotations**
You can annotate classes with @Component, @Service, @Repository, or @Controller. Spring automatically detects these beans if @ComponentScan is enabled.

???+ example "Annotations Example"
    ```java
    @Component
    public class PaymentService { }

    @Component
    public class OrderService {
        private final PaymentService paymentService;

        @Autowired
        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    }
    ```

    ```java
    @Configuration
    @ComponentScan(basePackages = "com.example")
    public class AppConfig { }
    ```

### **Bean Scopes**

The scope of a bean defines the lifecycle and visibility of that bean within the Spring IoC container.

#### **Types of Bean Scopes**

**singleton (default):** A single instance of the bean is created and shared across the entire application, Used for stateless beans.
???+ example
    ```java
    @Scope("singleton")
    @Component
    public class SingletonBean { }
    ```


**prototype:** A new instance is created every time the bean is requested, Useful for stateful objects or temporary tasks.
???+ example
    ```java
    @Scope("prototype")
    @Component
    public class PrototypeBean { }
    ```
    

**request:** A new bean instance is created for each HTTP request, Used in web applications.    

**session:** A single instance is created per HTTP session.

**globalSession:** A global session scope for applications using portlets.


### **Bean Lifecycle**

Each Spring bean goes through several lifecycle phases, starting from instantiation to destruction. The Spring IoC container manages this lifecycle internally.


!!! info "Bean Lifecycle Phases"
    - Bean Instantiation: The IoC container instantiates the bean using the constructor or factory method.

    - Dependency Injection (DI): The container injects dependencies (via constructor, setter, or field injection).

    - Custom Initialization: If the bean implements `InitializingBean` or uses the `@PostConstruct` annotation, the initialization logic is executed here.

    - Bean Ready to Use: After initialization, the bean is ready for use by the application.

    - Custom Destruction: When the IoC container is shutting down, beans with `@PreDestroy` or `DisposableBean` are given a chance to release resources.


### **Bean Lifecycle Callbacks**

**InitializingBean Interface:** If a bean implements the `InitializingBean` interface, it must override the `afterPropertiesSet()` method, which is called after all properties are set.

???+ example
    ```java
    public class MyService implements InitializingBean {
        @Override
        public void afterPropertiesSet() {
            System.out.println("MyService is initialized.");
        }
    }
    ```

**DisposableBean Interface:** If a bean implements the `DisposableBean` interface, it must override the `destroy()` method, which is called during the destruction phase.

???+ example
    ```java
    public class MyService implements DisposableBean {
        @Override
        public void destroy() {
            System.out.println("MyService is being destroyed.");
        }
    }
    ```

**Using `@PostConstruct` and `@PreDestroy`:** These annotations are the recommended way to manage initialization and destruction callbacks.

???+ example
    ```java
    @Component
    public class MyService {

        @PostConstruct
        public void init() {
            System.out.println("Initialization logic in @PostConstruct.");
        }

        @PreDestroy
        public void cleanup() {
            System.out.println("Cleanup logic in @PreDestroy.");
        }
    }
    ```

**Custom Initialization and Destruction Methods:** You can also specify custom methods in the bean configuration.

???+ example "Example (Java Config)"
    ```java
    @Bean(initMethod = "init", destroyMethod = "cleanup")
    public MyService myService() {
        return new MyService();
    }
    ```


### **Eager vs. Lazy Initialization**

#### Eager Initialization
All singleton beans are created at the time of application startup (default behavior), Useful for performance since all dependencies are resolved upfront.

#### Lazy Initialization
Beans are created only when they are first requested, You can enable lazy initialization at the bean level using `@Lazy`.

???+ example
    ```java
    @Lazy
    @Component
    public class LazyBean { }
    ```

### **DI and Bean Relationships**

Spring IoC container resolves bean dependencies through constructor injection, setter injection, or field injection, You can specify bean dependencies explicitly using the `depends-on` attribute in XML.

???+ example "Example (XML)"
    ```xml
    <bean id="databaseConnection" class="com.example.DatabaseConnection"/>
    <bean id="orderService" class="com.example.OrderService" depends-on="databaseConnection"/>
    ```

This ensures that the `databaseConnection` bean is initialized before the `orderService` bean.


### **Beans Circular Dependencies**

A circular dependency occurs when two or more beans are mutually dependent on each other.

???+ example
    ```java
    @Component
    public class A {
        @Autowired
        private B b;
    }

    @Component
    public class B {
        @Autowired
        private A a;
    }
    ```

Spring tries to resolve circular dependencies using singleton beans by injecting proxies, but it fails for constructor injection. To avoid circular dependencies:
- Refactor the code to reduce dependencies.
- Use setter injection instead of constructor injection.


### **Bean Definition Inheritance**

Spring allows bean definitions to inherit properties from a parent bean. This helps reduce configuration duplication.

???+ example "Example (XML)"
    ```xml
    <bean id="parentBean" class="com.example.BaseService">
        <property name="name" value="Base Service"/>
    </bean>

    <bean id="childBean" class="com.example.ChildService" parent="parentBean">
        <property name="name" value="Child Service"/>
    </bean>
    ```


## **Aspect-Oriented Programming**

AOP allows you to separate cross-cutting concerns (like logging, security, or transaction management) from the business logic. In Spring, AOP is implemented using aspects, advice, and pointcuts.

- Aspect: A module that encapsulates cross-cutting logic.
- Advice: Code that runs at a specific point (before, after, or around a method).
- Pointcut: Defines where the advice applies.
- Weaving: The process of applying aspects to target objects.

???+ example "AOP Example in Spring"

    ```java title="Define an Aspect"
    @Aspect
    @Component
    public class LoggingAspect {
        @Before("execution(* com.example.*.*(..))")
        public void logBefore(JoinPoint joinPoint) {
            System.out.println("Before method: " + joinPoint.getSignature().getName());
        }
    }
    ```

    ```java title="Enable AOP"
    @Configuration
    @EnableAspectJAutoProxy
    public class AppConfig { }
    ```


## **Spring Events**

Spring supports an event-driven model that allows you to build decoupled components. The ApplicationContext can publish events and allow listeners to respond to them.

???+ example "Example of a Custom Event"
    ```java
    public class CustomEvent extends ApplicationEvent {
        public CustomEvent(Object source) {
            super(source);
        }
    }
    ```

    ```java
    @Component
    public class CustomEventListener implements ApplicationListener<CustomEvent> {
        @Override
        public void onApplicationEvent(CustomEvent event) {
            System.out.println("Received custom event: " + event);
        }
    }
    ```


## **Spring Expression Lang (SpEL)**

SpEL allows you to manipulate and query beans dynamically. It can be used inside XML or annotations.

???+ example "Example of SpEL"
    ```xml
    <bean id="myBean" class="com.example.MyClass">
        <property name="value" value="#{2 + 3}"/>
    </bean>
    ```

## **Summary**

- Spring Core simplifies Java development with IoC and DI, promoting loose coupling and testability.
- IoC Container manages the creation and lifecycle of beans, ensuring efficient dependency management.
- AOP adds modularity by separating cross-cutting concerns.
- SpEL provides dynamic querying and manipulation of beans.
- ApplicationContext enhances the BeanFactory with additional features, like event propagation and i18n support.

---