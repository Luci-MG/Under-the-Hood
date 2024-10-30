# **Decorator Design Pattern**

The **Decorator Pattern** is a structural design pattern that allows behavior to be added to individual objects, either statically or dynamically, without affecting the behavior of other objects from the same class. This pattern is particularly useful when you need to add functionality to objects without subclassing and in scenarios where multiple combinations of behaviors are required.

---

### **What is the Decorator Pattern?**

The **Decorator Pattern** is used to attach additional responsibilities or behaviors to an object dynamically. It wraps the original object, adding new behavior while keeping the object’s interface intact. A decorator class has a reference to the original object and implements the same interface.

---

#### **Key Concepts:**
- **Component**: The interface or abstract class that defines the original object.
- **ConcreteComponent**: The actual object that implements the `Component` interface.
- **Decorator**: An abstract class that implements the `Component` interface and holds a reference to a `Component` object.
- **Concrete Decorators**: Subclasses of the `Decorator` that add specific functionalities.

---

### **When to Use the Decorator Pattern?**

- When you need to **add responsibilities to individual objects dynamically** and without altering their structure.
- When you want to **extend the functionality of an object at runtime**, without changing the class.
- When **multiple combinations of responsibilities** are needed, and subclassing would result in too many subclasses.
- When you want to **keep the original class unchanged** but extend its behavior transparently.

---

### **Example of Decorator Pattern in Java**

Let's consider an example where we are building a **coffee shop**. Different types of coffees can be enhanced with add-ons like milk, sugar, etc. Using the decorator pattern, we can apply these add-ons dynamically without subclassing.

```java
// Step 1: Component Interface
public interface Coffee {
    String getDescription();
    double getCost();
}

// Step 2: ConcreteComponent (Basic Coffee)
public class BasicCoffee implements Coffee {
    @Override
    public String getDescription() {
        return "Basic Coffee";
    }

    @Override
    public double getCost() {
        return 2.0;
    }
}

// Step 3: Decorator (Abstract)
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee coffee; // The object being decorated

    public CoffeeDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    public String getDescription() {
        return coffee.getDescription();
    }

    public double getCost() {
        return coffee.getCost();
    }
}

// Step 4: Concrete Decorators (e.g., Milk, Sugar)
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Milk";
    }

    @Override
    public double getCost() {
        return coffee.getCost() + 0.5;
    }
}

public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public String getDescription() {
        return coffee.getDescription() + ", Sugar";
    }

    @Override
    public double getCost() {
        return coffee.getCost() + 0.2;
    }
}

// Step 5: Usage
public class CoffeeShop {
    public static void main(String[] args) {
        Coffee coffee = new BasicCoffee();
        System.out.println(coffee.getDescription() + " $" + coffee.getCost());

        coffee = new MilkDecorator(coffee);
        System.out.println(coffee.getDescription() + " $" + coffee.getCost());

        coffee = new SugarDecorator(coffee);
        System.out.println(coffee.getDescription() + " $" + coffee.getCost());
    }
}
```

**Output:**
```
Basic Coffee $2.0
Basic Coffee, Milk $2.5
Basic Coffee, Milk, Sugar $2.7
```

---

#### **Advantages of the Decorator Pattern:**

- **Open for extension, closed for modification**: New functionalities can be added without altering existing code.
- **More flexible** than inheritance. It allows the combination of behaviors at runtime.
- Helps achieve **single responsibility principle** since each decorator adds only one type of behavior.

#### **Disadvantages:**

- **Complexity increases** with a large number of decorators.
- Debugging can be challenging because you need to trace through multiple layers of decorators.

---

### **How to Use Decorator Pattern with Spring Boot?**

In **Spring Boot**, the decorator pattern can be used in scenarios such as **logging, monitoring, or security checks**. You can implement a decorator pattern to enhance service classes without changing their core logic. Here's an example where we decorate a service class to add **logging functionality**.

---

#### **Example of Decorator in Spring Boot**

1. **Component Interface (Service Layer):**
```java
public interface UserService {
    String getUserDetails(String userId);
}
```

2. **Concrete Component:**
```java
@Service
public class UserServiceImpl implements UserService {
    @Override
    public String getUserDetails(String userId) {
        return "User details for " + userId;
    }
}
```

3. **Decorator:**
```java
@Service
public class LoggingUserService implements UserService {

    private final UserService userService;

    public LoggingUserService(UserService userService) {
        this.userService = userService;
    }

    @Override
    public String getUserDetails(String userId) {
        System.out.println("Fetching details for user: " + userId);
        return userService.getUserDetails(userId);
    }
}
```

4. **Configuration to Use Decorator:**
```java
@Configuration
public class ServiceConfig {

    @Bean
    public UserService userService(UserServiceImpl userServiceImpl) {
        return new LoggingUserService(userServiceImpl);
    }
}
```

---

#### **How it Works in Spring Boot:**

- **`UserServiceImpl`** provides the basic functionality.
- **`LoggingUserService`** acts as a decorator, adding logging before calling the original method.
- Spring’s **DI (Dependency Injection)** ensures that the decorated service is injected wherever needed.

---

#### **Where Not to Use the Decorator Pattern?**

- If **too many layers of decorators** are required, it can make code complex and difficult to debug.
- When **performance** is a concern, as the decorator adds overhead with each layer.
- If the **object’s behavior doesn’t need to change dynamically**, then simpler design patterns like **inheritance or composition** might suffice.
- In cases where **direct modification** to the existing class is possible, decorators might be overkill.

---

### **Summary:**

The **Decorator Pattern** is a powerful and flexible way to enhance objects with additional behaviors dynamically without altering their structure. It shines in scenarios requiring combinations of behaviors and helps maintain clean, modular code. 

In **Spring Boot**, it can be used for decorating services with additional features like **logging, security, or metrics**, allowing these aspects to remain separate from the core business logic.

This pattern should be used thoughtfully since excessive use can introduce complexity and make debugging difficult. However, when applied correctly, it ensures that code remains extensible and adheres to the **Single Responsibility Principle** and **Open/Closed Principle**.

---