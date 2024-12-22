# **SOLID Principles**

## **Single Responsibility Principle**  
A class should have only one reason to change. This means that each class should focus on a single responsibility or feature.

??? danger "Violation Example"
    ```java
    // Violates SRP: User class has multiple responsibilities.
    public class User {
        private String name;
        private String email;

        public void sendEmail(String message) {
            // Sending email logic here...
            System.out.println("Email sent to " + email);
        }

        public void saveUser() {
            // Save user to the database
            System.out.println("User saved to DB");
        }
    }
    ```

??? Success "Fixed Example"
    ```java 
    // Separate responsibilities into different classes.
    public class User {
        private String name;
        private String email;

        // Getters and setters...
    }

    public class UserRepository {
        public void save(User user) {
            System.out.println("User saved to DB");
        }
    }

    public class EmailService {
        public void sendEmail(User user, String message) {
            System.out.println("Email sent to " + user.getEmail());
        }
    }
    ```

---

## **Open Closed Principle**  
Software components (classes, functions, etc.) should be open for extension but closed for modification. You shouldn’t modify existing code to add new behavior instead, extend it.

??? danger "Violation Example"
    ```java
    // Violates OCP: PaymentProcessor needs to be modified for new payment types.
    public class PaymentProcessor {
        public void pay(String type) {
            if (type.equals("credit")) {
                System.out.println("Processing credit card payment...");
            } else if (type.equals("paypal")) {
                System.out.println("Processing PayPal payment...");
            }
        }
    }
    ```

??? success "Fixed Example"
    ```java
    // Use an interface for extensibility.
    interface PaymentMethod {
        void pay();
    }

    public class CreditCardPayment implements PaymentMethod {
        public void pay() {
            System.out.println("Processing credit card payment...");
        }
    }

    public class PayPalPayment implements PaymentMethod {
        public void pay() {
            System.out.println("Processing PayPal payment...");
        }
    }

    public class PaymentProcessor {
        public void processPayment(PaymentMethod paymentMethod) {
            paymentMethod.pay();
        }
    }
    ```

---

## **Liskov Substitution Principle**  
Subclasses should be substitutable for their base class without altering the correctness of the program.

??? danger "Violation Example"
    ```java
    // Violates LSP: Square changes the behavior of Rectangle.
    class Rectangle {
        protected int width, height;

        public void setWidth(int width) {
            this.width = width;
        }

        public void setHeight(int height) {
            this.height = height;
        }

        public int getArea() {
            return width * height;
        }
    }

    class Square extends Rectangle {
        @Override
        public void setWidth(int width) {
            this.width = width;
            this.height = width;  // Violates LSP: Unexpected behavior.
        }

        @Override
        public void setHeight(int height) {
            this.width = height;
            this.height = height;
        }
    }
    ```

??? success "Fixed Example"
    ```java
    // Use separate classes to maintain correct behavior.
    class Shape {
        public int getArea() {
            return 0;
        }
    }

    class Rectangle extends Shape {
        protected int width, height;

        public Rectangle(int width, int height) {
            this.width = width;
            this.height = height;
        }

        @Override
        public int getArea() {
            return width * height;
        }
    }

    class Square extends Shape {
        private int side;

        public Square(int side) {
            this.side = side;
        }

        @Override
        public int getArea() {
            return side * side;
        }
    }
    ```

---

## **Interface Segregation Principle**
A client should not be forced to implement interfaces that it does not use. Instead, smaller, more specific interfaces should be preferred.

??? danger "Violation Example"
    ```java
    // Violates ISP: Cat(r) needs to implement unnecessary methods.
    interface Vehicle {
        void drive();
        void fly();
    }

    class Car implements Vehicle {
        @Override
        public void drive() {
            System.out.println("Car is driving...");
        }

        @Override
        public void fly() {
            // Car can't fly! This method is unnecessary.
            throw new UnsupportedOperationException("Car can't fly");
        }
    }
    ```

??? success "Fixed Example"
    ```java
    // Use separate interfaces for each capability.
    interface Drivable {
        void drive();
    }

    interface Flyable {
        void fly();
    }

    class Car implements Drivable {
        @Override
        public void drive() {
            System.out.println("Car is driving...");
        }
    }

    class Plane implements Drivable, Flyable {
        @Override
        public void drive() {
            System.out.println("Plane is taxiing...");
        }

        @Override
        public void fly() {
            System.out.println("Plane is flying...");
        }
    }
    ```

---

## **Dependency Inversion Principle**
High-level modules should not depend on low-level modules. Both should depend on abstractions.

??? danger "Violation Example"
    ```java
    // Violates DIP: High-level class depends on a specific implementation.
    class SQLDatabase {
        public void connect() {
            System.out.println("Connected to SQL Database");
        }
    }

    class Application {
        private SQLDatabase database;

        public Application() {
            database = new SQLDatabase();  // Tight coupling to SQLDatabase.
        }

        public void start() {
            database.connect();
        }
    }
    ```

??? success "Fixed Example"
    ```java
    // Depend on an abstraction instead of a specific implementation.
    interface Database {
        void connect();
    }

    class SQLDatabase implements Database {
        public void connect() {
            System.out.println("Connected to SQL Database");
        }
    }

    class NoSQLDatabase implements Database {
        public void connect() {
            System.out.println("Connected to NoSQL Database");
        }
    }

    class Application {
        private Database database;

        public Application(Database database) {
            this.database = database;
        }

        public void start() {
            database.connect();
        }
    }
    ```

---

## **Summary**

| **Principle**                       | **Definition**                                              | **Violation Example**                           | **Fixed Example**                                  |
|--------------------------------------|--------------------------------------------------------------|------------------------------------------------|---------------------------------------------------|
| **Single Responsibility**      | A class should have only one reason to change.               | User class manages both data and emails.       | Separate `User`, `EmailService`, `UserRepository`.|
| **Open Closed**                | Open for extension, closed for modification.                 | Modify `PaymentProcessor` for new methods.     | Use `PaymentMethod` interface and extend classes. |
| **Liskov Substitution**        | Subtypes should behave like their base type.                 | `Square` modifies behavior of `Rectangle`.     | Separate `Square` and `Rectangle` classes.        |
| **Interface Segregation**      | Use small, specific interfaces.                              | Car implements unnecessary `fly()` method.     | Split into `Drivable` and `Flyable` interfaces.   |
| **Dependency Inversion**       | Depend on abstractions, not implementations.                 | App depends on `SQLDatabase` directly.         | Use `Database` interface for loose coupling.      |

---