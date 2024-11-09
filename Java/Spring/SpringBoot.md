# **Spring Boot** 

How Spring Boot automates configurations, deals with microservices, and manages monitoring, security, and performance.

---

## **1. What is Spring Boot?**

**Spring Boot** is an extension of the Spring Framework that simplifies the development of Java applications by offering:
1. **Convention over Configuration**: Minimizes manual configurations.
2. **Embedded Server Support**: Includes servers like Tomcat/Jetty for quick setups.
3. **Auto-Configuration**: Automatically configures your application based on included dependencies.
4. **Production-ready Tools**: Provides health monitoring, metrics, and more with **Spring Boot Actuator**.

The goal of Spring Boot is to help developers build stand-alone, production-grade applications quickly and with less fuss.

---

## **2. Spring Boot Application Architecture**

A Spring Boot application consists of:
1. **Spring Boot Starters**: Dependency bundles for specific use cases (e.g., `spring-boot-starter-web` for web apps).
2. **Auto-Configuration**: Based on what is in the classpath (e.g., if JPA libraries are present, Spring Boot configures a DataSource and EntityManagerFactory).
3. **Embedded Servers**: Applications run directly with Tomcat, Jetty, or Undertow.
4. **Application Properties**: Customizable settings using `application.properties` or `application.yml`.

---

## **3. Spring Boot Key Components in Depth**

### 3.1 **Annotations in Spring Boot**

1. **`@SpringBootApplication`**:
   - Combines three core annotations:
     - **`@Configuration`**: Marks the class as a source of bean definitions.
     - **`@EnableAutoConfiguration`**: Enables auto-configuration based on classpath contents.
     - **`@ComponentScan`**: Scans for Spring components (beans, services, controllers).

2. **`@RestController`**: Marks a class as a controller with REST endpoints.

3. **`@Autowired`**: Injects dependencies automatically by type.

4. **`@Value`**: Injects values from properties files.

5. **`@Profile`**: Activates beans only under specific profiles (e.g., dev, prod).

### 3.2 **Starters in Spring Boot**
- Starters are **pre-configured dependency bundles** for common functionalities:
   - `spring-boot-starter-web`: For web applications.
   - `spring-boot-starter-data-jpa`: For working with databases using JPA.
   - `spring-boot-starter-security`: For authentication and authorization.
   - `spring-boot-starter-actuator`: For monitoring and management.

### 3.3 **How Auto-Configuration Works**
Spring Boot uses **`@EnableAutoConfiguration`** to detect dependencies and automatically configure beans for you. For example:
- If `spring-boot-starter-data-jpa` is present, it will:
  1. Configure a `DataSource`.
  2. Configure an `EntityManagerFactory` to manage JPA entities.
  3. Enable transaction management using `@EnableTransactionManagement`.

**How to Debug Auto-Configuration:**
- Add `--debug` to the application start command to list all auto-configurations.
  ```
  $ java -jar myapp.jar --debug
  ```

- If you need to exclude an auto-configuration, use:
  ```java
  @SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})
  ```

---

## **4. Spring Boot Application Lifecycle**

1. **Startup**:
   - Spring Boot applications initialize with **SpringApplication.run()**.
   - The lifecycle involves loading beans, initializing contexts, and wiring dependencies.
   
2. **Embedded Server**:
   - By default, Spring Boot uses **Tomcat** as the embedded server. Others include Jetty and Undertow.
   - The server listens on a configurable port (default: `8080`).

3. **Shutdown**:
   - Spring Boot provides **graceful shutdown** using `@PreDestroy` or hooks via `SpringApplication.addShutdownHook()`.

---

## **5. Spring Boot Configuration in Depth**

### 5.1 **Using `application.properties` and `application.yml`**
Spring Boot applications are configured using either of these files.

**Examples of `application.properties`:**
```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
```

**Using Profiles (e.g., Dev vs. Prod) in `application.yml`:**
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

You can activate profiles programmatically or through command-line options:
```
$ java -Dspring.profiles.active=prod -jar myapp.jar
```

---

### 5.2 **Custom Configuration Properties**
You can define your own custom properties and inject them into beans using `@ConfigurationProperties`.

```java
@ConfigurationProperties(prefix = "custom")
public class CustomConfig {
    private String name;
    private int timeout;

    // Getters and Setters
}
```

**application.properties:**
```properties
custom.name=SpringApp
custom.timeout=5000
```

Inject the `CustomConfig` bean:
```java
@Autowired
private CustomConfig customConfig;
```

---

## **6. Embedded Server Customization**
You can customize the embedded server by configuring the `EmbeddedServletContainerFactory`.

Example: Changing the Tomcat thread pool size:
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

## **7. Actuator and Monitoring**

### 7.1 **Spring Boot Actuator**
- Exposes application management and monitoring endpoints.
- Some common endpoints:
  - **`/actuator/health`**: Health status.
  - **`/actuator/info`**: Application information.
  - **`/actuator/metrics`**: Metrics data (e.g., memory usage, request counts).

**Securing Actuator Endpoints:**
```yaml
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

## **8. Security in Spring Boot**

1. **Basic Authentication Setup**:
   - Add `spring-boot-starter-security` dependency.
   - By default, Spring Boot enables basic authentication with a generated password.

2. **Custom Authentication**:
   ```java
   @Configuration
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
               .antMatchers("/public/**").permitAll()
               .anyRequest().authenticated()
               .and()
               .httpBasic();
       }
   }
   ```

3. **JWT Authentication**: 
   Use **JSON Web Tokens (JWT)** for token-based authentication in microservices architectures.

---

## **9. Building Microservices with Spring Boot**

1. **Service Registration and Discovery**: Use **Eureka** for service discovery.
2. **Load Balancing**: Use **Spring Cloud LoadBalancer** or **Netflix Ribbon**.
3. **API Gateway**: Use **Spring Cloud Gateway** for routing and security.
4. **Resilience**: Implement **Circuit Breakers** using **Resilience4j** or **Hystrix**.

---

## **10. Testing in Spring Boot**

1. **Unit Testing**:
   - Use `@SpringBootTest` for integration testing.
   - Mock beans with `@MockBean`.

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

2. **Test Containers**: Use **Testcontainers** for running databases or services in Docker containers during tests.

---

## **Summary**

Spring Boot streamlines the development process by providing **auto-configuration**, **embedded servers**, and a **production-ready environment**. It empowers developers to build and deploy microservices quickly, backed by powerful features like **Spring Security**, **Spring Data**, **Actuator**, and more. With its opinionated defaults and deep customizability, Spring Boot strikes a balance between simplicity and flexibility making it ideal for both beginners and advanced developers.

---