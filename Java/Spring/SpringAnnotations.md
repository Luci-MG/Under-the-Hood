# Spring Annotations.

A **comprehensive list of Spring Boot annotations**, covering **core Spring Boot, configuration, web, data, testing, and more**. I'll organize them by categories with **keys (annotation names)** and **values (purpose/use cases)** for easy reference.

---

## **1. Core Annotations in Spring Boot**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@SpringBootApplication`      | Main entry point for a Spring Boot application. Combines `@Configuration`, `@ComponentScan`, and `@EnableAutoConfiguration`. |
| `@EnableAutoConfiguration`    | Enables automatic configuration of Spring beans based on the classpath and defined properties. |
| `@ComponentScan`              | Scans the package and its sub-packages for Spring components (e.g., `@Component`, `@Service`). |
| `@Configuration`              | Marks a class as a source of bean definitions. Used to define Spring beans programmatically. |
| `@Bean`                       | Declares a method as a Spring bean, registered in the application context. |
| `@Import`                     | Imports additional configuration classes. |
| `@ImportResource`             | Loads bean definitions from external XML configuration files. |

---

## **2. Web and REST Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@RestController`             | Marks a class as a REST API controller. Combines `@Controller` and `@ResponseBody`. |
| `@Controller`                 | Marks a class as a web controller. Works with view templates (like Thymeleaf). |
| `@RequestMapping`             | Maps HTTP requests to specific handler methods or classes. Can be used on classes or methods. |
| `@GetMapping`                 | Maps HTTP GET requests to specific handler methods. |
| `@PostMapping`                | Maps HTTP POST requests to specific handler methods. |
| `@PutMapping`                 | Maps HTTP PUT requests to specific handler methods. |
| `@DeleteMapping`              | Maps HTTP DELETE requests to specific handler methods. |
| `@PatchMapping`               | Maps HTTP PATCH requests to specific handler methods. |
| `@RequestBody`                | Binds the HTTP request body to a Java object. Used in REST controllers. |
| `@ResponseBody`               | Binds the return value of a method directly to the HTTP response body. |
| `@RequestParam`               | Binds HTTP query parameters to method arguments. |
| `@PathVariable`               | Binds URI template variables to method parameters. |
| `@RequestHeader`              | Binds HTTP request headers to method parameters. |
| `@CookieValue`                | Binds cookie values to method parameters. |
| `@ModelAttribute`             | Binds form data to a model object. |
| `@SessionAttributes`          | Declares session-scoped model attributes. |
| `@CrossOrigin`                | Enables cross-origin requests (CORS) for specific endpoints. |

---

## **3. Data Access (JPA, JDBC) Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@Entity`                    | Marks a class as a JPA entity. |
| `@Table`                     | Specifies the database table for a JPA entity. |
| `@Id`                        | Marks a field as the primary key of a JPA entity. |
| `@GeneratedValue`            | Specifies how the primary key value should be generated. |
| `@Column`                    | Specifies the mapping of a field to a database column. |
| `@OneToOne`                  | Establishes a one-to-one relationship between entities. |
| `@OneToMany`                 | Establishes a one-to-many relationship between entities. |
| `@ManyToOne`                 | Establishes a many-to-one relationship between entities. |
| `@ManyToMany`                | Establishes a many-to-many relationship between entities. |
| `@JoinColumn`                | Specifies the foreign key column for a relationship. |
| `@Query`                     | Defines a custom JPQL or SQL query on a repository method. |
| `@Transactional`             | Marks a method or class as transactional. Ensures ACID properties in data operations. |
| `@EnableJpaRepositories`     | Enables JPA repositories for data access. |
| `@Repository`                | Marks a class as a data repository. |
| `@EnableTransactionManagement`| Enables declarative transaction management. |

---

## **4. Security Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@EnableWebSecurity`          | Enables Spring Security for web applications. |
| `@EnableGlobalMethodSecurity`| Enables method-level security annotations like `@PreAuthorize` and `@PostAuthorize`. |
| `@PreAuthorize`               | Applies authorization logic before a method is invoked. |
| `@PostAuthorize`              | Applies authorization logic after a method has executed. |
| `@Secured`                   | Secures a method by roles (deprecated in favor of `@PreAuthorize`). |
| `@RolesAllowed`               | Specifies which roles are allowed to access a method. |
| `@WithMockUser`               | Simulates a user for testing security. |

---

## **5. Spring Boot Testing Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@SpringBootTest`             | Runs integration tests for a Spring Boot application. Loads the full application context. |
| `@WebMvcTest`                | Tests only web layer components (e.g., controllers). |
| `@DataJpaTest`               | Tests only JPA repositories. Configures an in-memory database. |
| `@MockBean`                  | Replaces a bean with a mock during tests. |
| `@SpyBean`                   | Replaces a bean with a spy during tests. |
| `@TestConfiguration`         | Provides additional bean configurations for tests. |
| `@BeforeEach`                | Runs before each test method in a test class. |
| `@AfterEach`                 | Runs after each test method in a test class. |

---

## **6. Configuration and Profiles Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@ConfigurationProperties`   | Binds external configuration properties to a Java bean. |
| `@EnableConfigurationProperties`| Enables support for `@ConfigurationProperties` beans. |
| `@Profile`                   | Specifies the profile under which a bean is active (e.g., dev, prod). |
| `@Value`                     | Injects a value from the properties or environment. |
| `@PropertySource`            | Loads properties from an external file. |
| `@Environment`               | Provides access to the current environment settings. |

---

## **7. Actuator and Metrics Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@Endpoint`                  | Defines a custom Actuator endpoint. |
| `@ReadOperation`             | Marks a method as a read operation for an Actuator endpoint. |
| `@WriteOperation`            | Marks a method as a write operation for an Actuator endpoint. |
| `@DeleteOperation`           | Marks a method as a delete operation for an Actuator endpoint. |
| `@Timed`                     | Measures the execution time of a method. |
| `@Gauge`                     | Exposes a gauge metric to Actuator. |
| `@Metered`                   | Marks a method to be counted as a metric (deprecated in favor of `@Timed`). |

---

## **8. Spring Cloud and Microservices Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@EnableDiscoveryClient`     | Enables service registration with Eureka, Consul, or Zookeeper. |
| `@EnableFeignClients`        | Enables Feign clients for inter-service communication. |
| `@CircuitBreaker`            | Implements circuit-breaking logic using Resilience4j. |
| `@Retryable`                 | Enables retry logic for a method. |
| `@LoadBalanced`              | Enables load balancing for REST clients. |

---

## **9. Miscellaneous Annotations**
| **Annotation**               | **Purpose/Use Case**                                                                                           |
|------------------------------|----------------------------------------------------------------------------------------------------------------|
| `@Conditional`               | Conditionally registers a bean based on custom logic. |
| `@Async`                     | Marks a method to run asynchronously. |
| `@Scheduled`                 | Schedules a method to run at fixed intervals or cron expressions. |
| `@EventListener`             | Marks a method to listen for application events. |
| `@Cacheable`                 | Caches the result of a method. |
| `@CacheEvict`                | Evicts entries from a cache. |

---

## **Summary**

This is a **comprehensive list of all major Spring Boot annotations**, categorized by their functionality. With these annotations, Spring Boot makes it easier to develop applications by reducing boilerplate code, automating configuration, and offering powerful tools for testing, security, and microservices development.

---