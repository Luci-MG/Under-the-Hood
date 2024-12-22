# **Factory Design Method**

## **What ?**

Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created (decide which class to instantiate). This pattern delegates the responsibility of object creation to subclasses rather than using a direct constructor call, It is one of the most widely used creational design patterns, It helps in the creation of objects without specifying the exact class of the object that will be created.

!!! tip ""
    You provide a "factory" method that the client code calls to get the object, but the actual object that gets created is determined at runtime (based on some logic).


## **When to Use ?**
- When you don't know in advance which class to instantiate (i.e., you want the logic to determine the object type dynamically).
- When you need flexibility in creating objects but want to avoid tight coupling.
- When new types of objects might be introduced in the future without breaking code.
- When working with complex object creation logic (e.g., when object creation requires conditional logic).


## **When Not to Use ?**
- When only a single type of object needs to be created (Factory adds unnecessary complexity).
- If object creation does not require polymorphism or dynamism, and the same class instantiation works fine.
- When performance is critical, as Factory Method can introduce overhead by adding extra layers of abstraction.


## **Where it Shines ?**
- Many frameworks (like JDBC, Hibernate, etc.) rely on Factory Method to return specific instances without the client needing to know the class type.
- When there are multiple subclasses of a product and only one of them needs to be instantiated at runtime.
- If new product types might be introduced in the future, the pattern provides an easy way to extend.


## **Advantages**
- Loose coupling, The controller doesn't need to know exact type of object being created.
- Flexibility, You can add new types of notifications without modifying the controller logic.
- Testability, The factory can be easily mocked or stubbed during unit tests.


## **Structure**

1. **Product Interface:** Defines the interface for the object being created.
2. **Concrete Product:** Implements the product interface.
3. **Creator:** Declares the factory method which returns an object of type Product.
4. **Concrete Creator:** Overrides the factory method to return a specific product instance.


## **How To Implement ?**

??? example "Structured Example"

    ```java title="Step-1: Define a Product Interface"
    public interface Notification {
        void notifyUser();
    }
    ```

    ```java title="Step-2: Create Concrete Implementations of the Product"
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

    ```java title="Step-3: Create an Abstract Factory Class"
    public abstract class NotificationFactory {
        public abstract Notification createNotification();
    }
    ```

    ```java title="Step-4: Implement Concrete Factory Classes"
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

    ```java title="Step-5: Usage in Client Code"
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

??? example "Spring Boot Example"

    Spring Boot relies heavily on dependency injection (DI) and Inversion of Control (IoC), which means Spring beans can act as factory classes to produce the desired objects.

    #### **Example:** Notification Factory with Spring Boot

    ```java title="Define the Product Interface and Implementations (Same as Before)"
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

    ```java title="Create a Spring Factory Class"
    import org.springframework.stereotype.Service;
    // This class will act as the **Factory**. You can make it a Spring **`@Service` or `@Component`** bean, so Spring manages it.
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

    ```java title="Use the Factory Class in a Spring Controller"
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

    When you access `/notify/SMS` or `/notify/Email`, it will dynamically create and send the corresponding notification after running the spring boot application.


## **Summary**

The Factory Pattern enables dynamic object creation without specifying exact classes, reducing coupling and improving maintainability. It uses a factory method to determine which class to instantiate. In Spring Boot, this pattern integrates seamlessly through factory beans and dependency injection, providing flexible and condition-based object creation.

!!! note
    Factory Method is especially helpful in modular systems where new functionalities might be added frequently, and we want to minimize the impact of changes to existing code.

---