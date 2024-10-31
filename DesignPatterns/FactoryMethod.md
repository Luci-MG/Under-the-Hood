# **Factory Design Method**

The **Factory Method Pattern** is one of the most widely used **creational design patterns**. It helps in the creation of objects without specifying the exact class of the object that will be created. 


---

### **What is the Factory Method Pattern?**

**Factory Method** is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created.

The Factory Method Pattern defines an interface for creating an object but **lets subclasses decide which class to instantiate**. This pattern **delegates the responsibility of object creation to subclasses** rather than using a direct constructor call.

**Key Concept**:  
You provide a **"factory" method** that the client code calls to get the object, but the actual object that gets created is determined at runtime (based on some logic).

---

### **When to Use the Factory Method Pattern?**
- **When you don't know in advance which class to instantiate** (i.e., you want the logic to determine the object type dynamically).
- **When you need flexibility** in creating objects but want to avoid tight coupling with specific classes.
- **When new types of objects might be introduced** in the future without breaking existing code.
- **When working with complex object creation logic** (e.g., when object creation requires conditional logic).

---

### **Structure of Factory Method Pattern**

1. **Product Interface:** Defines the interface for the object being created.
2. **Concrete Product:** Implements the product interface.
3. **Creator:** Declares the factory method which returns an object of type Product.
4. **Concrete Creator:** Overrides the factory method to return a specific product instance.

---

### **Simple Java Example of Factory Method**

#### **Step 1: Define a Product Interface**
```java
public interface Notification {
    void notifyUser();
}
```

#### **Step 2: Create Concrete Implementations of the Product**
```java
public class SMSNotification implements Notification {
    @Override
    public void notifyUser() {
        System.out.println("Sending an SMS notification.");
    }
}

public class EmailNotification implements Notification {
    @Override
    public void notifyUser() {
        System.out.println("Sending an Email notification.");
    }
}
```

#### **Step 3: Create an Abstract Factory Class**
```java
public abstract class NotificationFactory {
    public abstract Notification createNotification();
}
```

#### **Step 4: Implement Concrete Factory Classes**
```java
public class SMSNotificationFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new SMSNotification();
    }
}

public class EmailNotificationFactory extends NotificationFactory {
    @Override
    public Notification createNotification() {
        return new EmailNotification();
    }
}
```

#### **Step 5: Usage in Client Code**
```java
public class Client {
    public static void main(String[] args) {
        NotificationFactory factory = new SMSNotificationFactory();
        Notification notification = factory.createNotification();
        notification.notifyUser();

        factory = new EmailNotificationFactory();
        notification = factory.createNotification();
        notification.notifyUser();
    }
}
```

---

### **When NOT to Use Factory Method Pattern**
- When **only a single type of object** needs to be created (Factory adds unnecessary complexity).
- If object creation does not require **polymorphism** or **dynamism**, and the same class instantiation works fine.
- When performance is critical, as Factory Method can introduce **overhead** by adding extra layers of abstraction.

---

### **Integrating Factory Method Pattern with Spring Boot**

Spring Boot relies heavily on **dependency injection (DI)** and **Inversion of Control (IoC)**, which means **Spring beans** can act as factory classes to produce the desired objects.

#### **Example: Notification Factory with Spring Boot**

##### **1. Define the Product Interface and Implementations (Same as Before)**
```java
public interface Notification {
    void notifyUser();
}

public class SMSNotification implements Notification {
    @Override
    public void notifyUser() {
        System.out.println("Sending an SMS notification.");
    }
}

public class EmailNotification implements Notification {
    @Override
    public void notifyUser() {
        System.out.println("Sending an Email notification.");
    }
}
```

##### **2. Create a Spring Factory Class**

This class will act as the **Factory**. You can make it a Spring **`@Service` or `@Component`** bean, so Spring manages it.

```java
import org.springframework.stereotype.Service;

@Service
public class NotificationFactory {
    public Notification createNotification(String type) {
        if (type.equalsIgnoreCase("SMS")) {
            return new SMSNotification();
        } else if (type.equalsIgnoreCase("Email")) {
            return new EmailNotification();
        }
        throw new IllegalArgumentException("Unknown notification type: " + type);
    }
}
```

##### **3. Use the Factory Class in a Spring Controller**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class NotificationController {

    @Autowired
    private NotificationFactory notificationFactory;

    @GetMapping("/notify/{type}")
    public String sendNotification(@PathVariable String type) {
        Notification notification = notificationFactory.createNotification(type);
        notification.notifyUser();
        return "Notification sent: " + type;
    }
}
```

##### **4. Run the Spring Boot Application**

When you access `/notify/SMS` or `/notify/Email`, it will dynamically create and send the corresponding notification.

---

### **Advantages of Using Factory Method in Spring Boot**
- **Loose coupling:** The controller doesn't need to know the exact type of object being created.
- **Flexibility:** You can add new types of notifications without modifying the controller logic.
- **Testability:** The factory can be easily **mocked or stubbed** during unit tests.

---

### **Where the Factory Method Pattern Shines**
1. **In frameworks:** Many frameworks (like JDBC, Hibernate, etc.) rely on Factory Method to return specific instances without the client needing to know the class type.
2. **When there are multiple subclasses** of a product and only one of them needs to be instantiated at runtime.
3. **If new product types** might be introduced in the future, the pattern provides an easy way to extend.

---

### **Summary of Factory Method Pattern**
- **What:** A pattern to create objects without specifying the exact class of object to be created.
- **When to Use:** When you need dynamic and flexible object creation.
- **Why to Use:** To reduce coupling and make the code more extensible and maintainable.
- **How to Implement:** By creating a factory method that decides which concrete class to instantiate.
- **Integration with Spring Boot:** Use factory beans and dependency injection to create the appropriate objects based on conditions.

This pattern is especially helpful in **modular systems** where new functionalities might be added frequently, and we want to **minimize the impact of changes** to existing code.

---

### Reference Links

1. https://refactoring.guru/design-patterns/factory-method

---