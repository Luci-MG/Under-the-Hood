# **Spring Boot**

This article covers how Spring Boot automates configurations, deals with microservices, and manages monitoring, security, and performance.

---

## **What is Spring Boot ?**

Spring Boot is an extension of the Spring Framework that simplifies the development of Java applications by offering:

- Convention over Configuration: Minimizes manual configurations.
- Embedded Server Support: Includes servers like Tomcat/Jetty for quick setups.
- Auto-Configuration: Automatically configures your application based on included dependencies.
- Production-ready Tools: Provides health monitoring, metrics, and more with Spring Boot Actuator.

The goal of Spring Boot is to help developers build stand-alone, production-grade applications quickly and with less fuss.

---

## **Application Architecture**

A Spring Boot application consists of

- Spring Boot Starters: Dependency bundles for specific use cases (e.g., `spring-boot-starter-web` for web apps).
- Auto-Configuration: Based on what is in the classpath (e.g., if JPA libraries are present, Spring Boot configures a DataSource and EntityManagerFactory).
- Embedded Servers: Applications run directly with Tomcat, Jetty, or Undertow.
- Application Properties: Customizable settings using `application.properties` or `application.yml`.

---

## **Key Components**

### **Annotations**

- `@SpringBootApplication`: Combines three core annotations
    - `@Configuration`: Marks the class as a source of bean definitions.
    - `@EnableAutoConfiguration`: Enables auto-configuration based on classpath contents.
    - `@ComponentScan`: Scans for Spring components (beans, services, controllers).

- `@RestController`: Marks a class as a controller with REST endpoints.

- `@Autowired`: Injects dependencies automatically by type.

- `@Value`: Injects values from properties files.

- `@Profile`: Activates beans only under specific profiles (e.g., dev, prod).

### **Starters**

Starters are pre-configured dependency bundles for common functionalities

- `spring-boot-starter-web`: For web applications.
- `spring-boot-starter-data-jpa`: For working with databases using JPA.
- `spring-boot-starter-security`: For authentication and authorization.
- `spring-boot-starter-actuator`: For monitoring and management.

### **How Auto-Config Works ?**
Spring Boot uses `@EnableAutoConfiguration` to detect dependencies and automatically configure beans for you. 

For example: If `spring-boot-starter-data-jpa` is present, it will

  1. Configure a `DataSource`.
  2. Configure an `EntityManagerFactory` to manage JPA entities.
  3. Enable transaction management using `@EnableTransactionManagement`.

How to Debug Auto-Configuration:

- Add `--debug` to the application start command to list all auto-configurations.
  ```
  $ java -jar myapp.jar --debug
  ```

- If you need to exclude an auto-configuration, use:
  ```java
  @SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
  ```

---

## **Application Lifecycle**

**Startup:** Spring Boot applications initialize with SpringApplication.run(), The lifecycle involves loading beans, initializing contexts, and wiring dependencies.
   
**Embedded Server:** By default, Spring Boot uses Tomcat as the embedded server. Others include Jetty and Undertow, The server listens on a configurable port (default: `8080`).

**Shutdown:** Spring Boot provides graceful shutdown using `@PreDestroy` or hooks via `SpringApplication.addShutdownHook()`.

---

## **Configuration in Depth**

### **Using `application.properties`**
Spring Boot applications are configured using either `application.properties`


???+ example "Examples of `application.properties`"
    ```properties
    server.port=8081
    spring.datasource.url=jdbc:mysql://localhost:3306/mydb
    spring.datasource.username=root
    spring.datasource.password=password
    ```

### **Using `application.yml`**

Using Profiles (e.g., Dev vs. Prod) in `application.yml`

???+ example "Example of `application.yml`"
      ```yaml
      server:
        port: 8080
      spring:
        profiles:
          active: dev

      ---
      spring:
        profiles: dev
        datasource:
          url: jdbc:h2:mem:testdb

      ---
      spring:
        profiles: prod
        datasource:
          url: jdbc:mysql://localhost:3306/proddb
      ```

You can activate profiles programmatically or through command-line options

```
$ java -Dspring.profiles.active=prod -jar myapp.jar
```

---

### **Custom Configuration**
You can define your own custom properties and inject them into beans using `@ConfigurationProperties`.

???+ example
    ```java
    @ConfigurationProperties(prefix = "custom")
    public class CustomConfig {
        private String name;
        private int timeout;

        // Getters and Setters
    }
    ```


    ```properties title="application.properties"
    custom.name=SpringApp
    custom.timeout=5000
    ```


    ```java title="Inject the CustomConfig bean"
    @Autowired
    private CustomConfig customConfig;
    ```

---

## **Embedded Server Customization**
You can customize the embedded server by configuring the `EmbeddedServletContainerFactory`.

???+ example "Changing the Tomcat thread pool size"
      ```java
      @Bean
      public ConfigurableServletWebServerFactory webServerFactory() {
          TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
          factory.setPort(9090);
          factory.addConnectorCustomizers(connector -> {
              connector.setAttribute("maxThreads", 200);
          });
          return factory;
      }
      ```

---

## **Actuator and Monitoring**

### **Spring Boot Actuator**

Exposes application management and monitoring endpoints.

Some common endpoints:

  - `/actuator/health`: Health status.
  - `/actuator/info`: Application information.
  - `/actuator/metrics`: Metrics data (e.g., memory usage, request counts).

```yaml title="Securing Actuator Endpoints"
management:
  endpoints:
    web:
      exposure:
        include: health, info
  security:
    enabled: true
```

You can customize or add your own metrics by using `@Timed` or `MeterRegistry`.

---

## **Security**

- Basic Authentication Setup: Add `spring-boot-starter-security` dependency, by default, Spring Boot enables basic authentication with a generated password.

- Custom Authentication
   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
               .antMatchers("/public/").permitAll()
               .anyRequest().authenticated()
               .and()
               .httpBasic();
       }
   }
   ```

- JWT Authentication: Use JSON Web Tokens (JWT) for token-based authentication in microservices architectures.

---

## **Building Microservices**

- Service Registration and Discovery: Use Eureka for service discovery.
- Load Balancing: Use Spring Cloud LoadBalancer or Netflix Ribbon.
- API Gateway: Use Spring Cloud Gateway for routing and security.
- Resilience: Implement Circuit Breakers using Resilience4j or Hystrix.

---

## **Testing**

- Unit Testing: Use `@SpringBootTest` for integration testing, Mock beans with `@MockBean`.
    ```java
    @SpringBootTest
    public class MyServiceTest {
        @MockBean
        private MyRepository repository;

        @Autowired
        private MyService service;

        @Test
        public void testService() {
            when(repository.findById(1L)).thenReturn(Optional.of(new MyEntity()));
            assertTrue(service.findById(1L).isPresent());
        }
    }
    ```

- Test Containers: Use Testcontainers for running databases or services in Docker containers during tests.

---

## Summary

Spring Boot streamlines the development process by providing auto-configuration, embedded servers, and a production-ready environment. It empowers developers to build and deploy microservices quickly, backed by powerful features like Spring Security, Spring Data, Actuator, and more. With its opinionated defaults and deep customizability, Spring Boot strikes a balance between simplicity and flexibility making it ideal for both beginners and advanced developers.

---