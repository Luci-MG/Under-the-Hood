[//]: # (TODO:: Need to add examples in other Language )

# **Strategy**

## **What ?** 

The Strategy Pattern is a behavioral design pattern that allows you to define a family of algorithms, encapsulate each one, and make them interchangeable. It lets the algorithm vary independently from clients that use it, promoting flexibility, scalability, and separation of concerns. 

In simpler terms, It enables selecting a specific algorithm at runtime based on the context, without modifying the client code. 


## **When to Use?**
- When you have multiple algorithms or behaviors that can be applied to an object and want to switch between them dynamically.
- When you need to avoid conditional logic (like many `if-else` or `switch` statements).
- When you want to decouple the code that uses the algorithm from the actual implementation of the algorithm.
- When a class has multiple related behaviors, and you want to configure them at runtime.


## **Why to Use ?**
- Avoids code duplication by eliminating repeated conditional logic.
- Adheres to the Open/Closed principle (open for extension, closed for modification).
- Promotes modularity and testability since algorithms are in separate classes.
- Simplifies maintenance by isolating algorithms from the business logic.
  

## **When Not to Use?**
- If the algorithms are not likely to change or only one behavior is needed, adding this pattern would add unnecessary complexity.
- When performance is critical, and switching algorithms frequently incurs overhead.
- If the algorithms are trivial and simple, the extra classes and abstractions may not be worth the effort.


## **How to Implement ?**

??? example "Simple Example"

    Let’s implement a payment system that allows different payment methods using the Strategy Pattern. We will define multiple payment strategies (like Credit Card and PayPal) and switch between them dynamically.

    ```java title="Step-1: Create the Strategy Interface"
    // PaymentStrategy.java
    public interface PaymentStrategy {
        void pay(int amount);
    }
    ```

    ```java title="Step-2: Implement Concrete Strategies"
    // CreditCardStrategy.java
    public class CreditCardStrategy implements PaymentStrategy {
        private String cardNumber;
        private String name;

        public CreditCardStrategy(String cardNumber, String name) {
            this.cardNumber = cardNumber;
            this.name = name;
        }

        @Override
        public void pay(int amount) {
            System.out.println(amount + " paid with credit card.");
        }
    }

    // PayPalStrategy.java
    public class PayPalStrategy implements PaymentStrategy {
        private String email;

        public PayPalStrategy(String email) {
            this.email = email;
        }

        @Override
        public void pay(int amount) {
            System.out.println(amount + " paid using PayPal.");
        }
    }
    ```

    ```java title="Step-3: Create a Context Class"
    // PaymentContext.java
    public class PaymentContext {
        private PaymentStrategy strategy;

        public PaymentContext(PaymentStrategy strategy) {
            this.strategy = strategy;
        }

        public void setPaymentStrategy(PaymentStrategy strategy) {
            this.strategy = strategy;
        }

        public void pay(int amount) {
            strategy.pay(amount);
        }
    }
    ```

    ```java title="Step-4: Test the Strategy Pattern"
    public class StrategyPatternDemo {
        public static void main(String[] args) {
            PaymentContext context = new PaymentContext(new CreditCardStrategy("1234-5678-9012", "John Doe"));
            context.pay(100);

            // Switch strategy at runtime
            context.setPaymentStrategy(new PayPalStrategy("john.doe@example.com"));
            context.pay(200);
        }
    }
    ```


??? example "Spring Boot Example"

    In a Spring Boot application, the Strategy Pattern can be applied by injecting different strategy implementations using Spring’s dependency injection.

    Let's build a simple notification service where the user can choose between sending notifications via Email or SMS.

    ```java title="Create the Strategy Interface"
    // NotificationStrategy.java
    public interface NotificationStrategy {
        void sendNotification(String message);
    }
    ```

    ```java title="Implement the Concrete Strategies"
    // EmailNotification.java
    import org.springframework.stereotype.Service;

    @Service("email")
    public class EmailNotification implements NotificationStrategy {
        @Override
        public void sendNotification(String message) {
            System.out.println("Sending Email: " + message);
        }
    }

    // SMSNotification.java
    import org.springframework.stereotype.Service;

    @Service("sms")
    public class SMSNotification implements NotificationStrategy {
        @Override
        public void sendNotification(String message) {
            System.out.println("Sending SMS: " + message);
        }
    }
    ```

    ```java title="Create a Context Class to Use the Strategy"
    // NotificationContext.java
    import org.springframework.stereotype.Component;

    @Component
    public class NotificationContext {

        private final Map<String, NotificationStrategy> strategies;

        public NotificationContext(Map<String, NotificationStrategy> strategies) {
            this.strategies = strategies;
        }

        public void send(String type, String message) {
            NotificationStrategy strategy = strategies.get(type);
            if (strategy == null) {
                throw new IllegalArgumentException("No such notification type");
            }
            strategy.sendNotification(message);
        }
    }
    ```

    ```java title="Create the Controller to Use the Notification Service"
    // NotificationController.java
    import org.springframework.web.bind.annotation.*;

    @RestController
    @RequestMapping("/notify")
    public class NotificationController {

        private final NotificationContext context;

        public NotificationController(NotificationContext context) {
            this.context = context;
        }

        @PostMapping("/{type}")
        public void sendNotification(@PathVariable String type, @RequestBody String message) {
            context.send(type, message);
        }
    }
    ```

    ```java title="Application Configuration and Running"
    // Application.java
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

    ??? info "How It Works"
        - NotificationContext uses a Map of NotificationStrategy beans, where the key is the bean name (like `email` or `sms`).
        - Spring Boot automatically injects the strategies with the `@Service` annotation.
        - In the **controller, the appropriate strategy is selected based on the URL path (`/notify/email` or `/notify/sms`).


??? example "Alternative Ways"

    **Using Enum-based Strategies:**  If the algorithms are simple and limited, you can use an `enum` with methods for strategy logic.

    ```java
    public enum Operation {
        ADD {
            @Override
            public int execute(int a, int b) {
                return a + b;
            }
        },
        SUBTRACT {
            @Override
            public int execute(int a, int b) {
                return a - b;
            }
        };

        public abstract int execute(int a, int b);
    }
    ```

    **Using Java 8 Lambdas:**  Since Java 8, you can use **lambdas** to avoid creating multiple strategy classes.

    ```java
    import java.util.function.Consumer;

    public class LambdaStrategyDemo {
        public static void main(String[] args) {
            Consumer<String> emailStrategy = message -> System.out.println("Email: " + message);
            Consumer<String> smsStrategy = message -> System.out.println("SMS: " + message);

            emailStrategy.accept("Hello via Email!");
            smsStrategy.accept("Hello via SMS!");
        }
    }
    ```


## **Summary**
The Strategy Pattern is a powerful way to manage dynamic behavior selection in a clean and decoupled way. In a Spring Boot application, you can easily integrate it by using dependency injection. However, it’s essential to use the pattern wisely to avoid unnecessary complexity or overhead. Use it when multiple behaviors or algorithms need to vary independently without modifying client code. Avoid using it if the added complexity is not justified.

---