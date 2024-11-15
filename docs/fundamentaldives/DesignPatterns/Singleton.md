# **Singleton Design Pattern**

## **What ?**

The **Singleton Pattern** is a **creational design pattern** that ensures a class has **only one instance** and provides a global access point to that instance. 

#### **Key Characteristics**
- **Single instance**: Only one object of the class is created.
- **Global access**: Provides a global point of access to that instance.

Singleton is useful when exactly **one instance of a class is needed** across the system, like for logging, configuration, database connection pools, etc.

---

## **When to Use ?**
- When an object holds configuration settings for the application.
- When only one connection pool should be active throughout the system.
- Ensures that all parts of the application use the same logging instance.
- When a cache should be available throughout the application.

---

## **When Not to Use ?**
- Singleton is limited to the JVM instance, so it won't work across multiple servers such as distributed systems.
- If the singleton object holds state, it can lead to **thread contention** in cases like High concurrency applications.
- Singleton classes are difficult to test because their state is shared across tests, leading to unpredictable behavior.

---

## **Why Use ?**
- As Only one instance is created, reducing memory overhead so memory efficiency 
- Ensures a consistent state throughout the application since all code accesses the same instance.
- Provides a way to access a shared resource or service from anywhere in the code.

---


## **How to Implement ?**

### **Eager Initialization**
The instance is created when the class is loaded. This is the simplest way, but it doesn’t support lazy loading.

``` java
public class EagerSingleton {
    private static final EagerSingleton INSTANCE = new EagerSingleton();

    // Private constructor to prevent instantiation
    private EagerSingleton() {}

    public static EagerSingleton getInstance() {
        return INSTANCE;
    }
}
```

!!! note
    When the instance is required throughout the application, and we are okay with it being created at startup.

---

### **Lazy Initialization**
The instance is created only when needed (on first access). But this version **is not thread-safe**.

```java
public class LazySingleton {
    private static LazySingleton instance;

    private LazySingleton() {}

    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}
```

!!! note
    Not suitable for multithreaded environments.

---

### **Using Synchronized**
This solves the issue of thread safety by synchronizing the access method.

```java
public class ThreadSafeSingleton {
    private static ThreadSafeSingleton instance;

    private ThreadSafeSingleton() {}

    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }
        return instance;
    }
}
```
!!! note
    Performance overhead due to synchronization.

---

### **Double-Checked Locking**
This improves the performance by reducing the overhead of synchronized block.

```java
public class DoubleCheckedLockingSingleton {
    private static volatile DoubleCheckedLockingSingleton instance;

    private DoubleCheckedLockingSingleton() {}

    public static DoubleCheckedLockingSingleton getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckedLockingSingleton.class) {
                if (instance == null) {
                    instance = new DoubleCheckedLockingSingleton();
                }
            }
        }
        return instance;
    }
}
```

---

### **Bill Pugh Singleton**
This approach leverages **static inner classes**, which ensures thread safety and lazy loading without synchronization overhead.

```java
public class BillPughSingleton {
    private BillPughSingleton() {}

    // Static inner class responsible for holding the instance
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }

    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

!!! note
    Best Practice

---

### **Enum Singleton**
This approach is the most concise and prevents issues with serialization and reflection attacks.

```java
public enum EnumSingleton {
    INSTANCE;

    public void someMethod() {
        System.out.println("Enum Singleton Instance");
    }
}
```

!!! note
    Recommended

---

### **With Spring Boot**

In **Spring Boot**, Spring’s **IoC container** (Inversion of Control) makes singleton beans by default. Each bean in Spring is, by default, a singleton. So, **you don’t need to explicitly implement the Singleton pattern**. Instead, you annotate the class with `@Component` or `@Service`, and Spring ensures that only one instance is created and managed.


```java title="Example in Spring Boot"
import org.springframework.stereotype.Component;

@Component
public class MySingletonService {
    public void doSomething() {
        System.out.println("Singleton service is working");
    }
}
```


```java title="How to use in a Spring Boot application"
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {

    private final MySingletonService singletonService;

    @Autowired
    public MyController(MySingletonService singletonService) {
        this.singletonService = singletonService;
    }

    @GetMapping("/test")
    public String test() {
        singletonService.doSomething();
        return "Check logs for Singleton Service";
    }
}
```
!!! note
    Spring manages the lifecycle and thread safety for you, ensuring it behaves like a Singleton without extra code.

---

### **Comparisons**

| **Implementation**                 | **Thread Safety** | **Lazy Initialization** | **Serialization Safe** | **Ease of Implementation** |
|------------------------------------|------------------|------------------------|-----------------------|-----------------------------|
| Eager Initialization               | Yes              | No                     | No                    | Easy                        |
| Lazy Initialization                | No               | Yes                    | No                    | Easy                        |
| Thread-safe Singleton (Synchronized) | Yes            | Yes                    | No                    | Moderate                    |
| Double-Checked Locking Singleton   | Yes              | Yes                    | No                    | Moderate                    |
| Bill Pugh Singleton                | Yes              | Yes                    | No                    | Best Practice               |
| Enum Singleton                     | Yes              | Yes                    | Yes                   | Recommended                 |

---

## **Potential Issues**
- If not implemented correctly, it can lead to thread synchronization issues.
- Normal singletons can break if the instance is serialized and deserialized.
- Code becomes tightly coupled to the singleton instance, reducing flexibility.
- It’s harder to mock or replace singletons in unit tests, leading to less modular code.

---

## **Summary**

The **Singleton Pattern** is a powerful tool when used appropriately. However, misuse can lead to tightly coupled code, concurrency issues, and testing difficulties. If you are working with Spring Boot, **rely on Spring’s built-in singleton beans** instead of implementing your own singleton logic. Where thread safety, serialization, or distributed behavior is required, choose the appropriate Singleton implementation like **Enum Singleton** or **Bill Pugh Singleton**.

!!! note
    By default, **a single instance of the bean** is created and **shared across the entire application** (singleton scope). If two or more components use the same bean, they will refer to **the same instance**.

    However, if you need a new instance every time a bean is requested, you can change the scope to `prototype`. But be mindful Spring’s singleton scope simplifies things like caching and state consistency, while prototype beans may introduce complexity.

---