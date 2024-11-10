# **Bridge Design Pattern**

The **Bridge Pattern** is a **structural design pattern** that helps to decouple an abstraction from its implementation so that both can vary independently. This pattern is especially useful when you need to manage complex class hierarchies or have multiple dimensions of variations.

### **Overview of Bridge Pattern**

- **Problem Addressed:** When both the abstraction (interface) and its implementation (how it works internally) need to evolve, the code becomes complex and hard to manage. Bridge helps to separate these concerns.

- **Key Concept:** The pattern separates the abstraction (interface) from the actual implementation and lets them evolve independently by delegating the concrete work to another interface.

---

### **How the Bridge Pattern Works**

The structure involves two key parts:

1. **Abstraction:** Defines the interface (or abstract class) which client code uses.
2. **Implementor:** The actual implementations are defined through composition, not inheritance.

The **Abstraction** contains a reference to the **Implementor** (interface or class). This lets the abstraction delegate the implementation details to the concrete implementations.

---

### **Class Diagram of Bridge Pattern**
```
Abstraction --> Implementor
     |                |
RefinedAbstraction   ConcreteImplementor
```
- **Abstraction**: This is an abstract class or interface that defines the methods which are implemented by Refined Abstraction.
- **Implementor**: This is an interface or abstract class that defines the implementation methods.
- **Refined Abstraction**: A concrete class that extends the Abstraction.
- **Concrete Implementor**: A concrete class that implements the Implementor interface.

---

### **Example of Bridge Pattern in Java**

Let’s look at a **real-world example**—rendering shapes on different platforms. The rendering logic could vary depending on the platform (Windows, Linux, etc.), but the shape (e.g., Circle, Rectangle) stays the same.

### **Implementation**

#### 1. Define the **Implementor** interface
```java
interface Renderer {
    void render(String shape);
}
```

#### 2. Create **Concrete Implementor** classes
```java
class VectorRenderer implements Renderer {
    @Override
    public void render(String shape) {
        System.out.println("Rendering " + shape + " as vectors.");
    }
}

class RasterRenderer implements Renderer {
    @Override
    public void render(String shape) {
        System.out.println("Rendering " + shape + " as pixels.");
    }
}
```

#### 3. Define the **Abstraction**
```java
abstract class Shape {
    protected Renderer renderer;

    public Shape(Renderer renderer) {
        this.renderer = renderer;
    }

    public abstract void draw();
}
```

#### 4. Create **Refined Abstraction** classes
```java
class Circle extends Shape {
    public Circle(Renderer renderer) {
        super(renderer);
    }

    @Override
    public void draw() {
        renderer.render("Circle");
    }
}

class Rectangle extends Shape {
    public Rectangle(Renderer renderer) {
        super(renderer);
    }

    @Override
    public void draw() {
        renderer.render("Rectangle");
    }
}
```

#### 5. Client Code
```java
public class BridgePatternDemo {
    public static void main(String[] args) {
        Shape circle = new Circle(new VectorRenderer());
        circle.draw(); // Output: Rendering Circle as vectors.

        Shape rectangle = new Rectangle(new RasterRenderer());
        rectangle.draw(); // Output: Rendering Rectangle as pixels.
    }
}
```

---

### **Where and When to Use the Bridge Pattern**

#### **When to Use:**
1. **Multiple Dimensions of Variation:** When your class needs to support multiple variations along two or more dimensions (like different types of shapes on different platforms).
2. **Decoupling:** When you want to decouple the abstraction from its implementation so that both can evolve independently.
3. **Avoiding Class Explosion:** If you have multiple variations, inheritance might lead to a combinatorial explosion of classes. For example, `CircleRed`, `CircleBlue`, `SquareRed`, `SquareBlue`—this can be avoided with the Bridge pattern.

#### **Where Not to Use:**
1. If the system has **simple inheritance hierarchies** where variations don’t overlap, the added complexity of Bridge isn’t necessary.
2. If **abstraction and implementation are unlikely to change**, simpler patterns (like inheritance) might be more suitable.

---

### **Using the Bridge Pattern in Spring Boot**

In a **Spring Boot** application, you can use the **Bridge pattern** to switch between different implementations of a service dynamically, such as switching between multiple ways of sending notifications (e.g., Email, SMS). This is helpful when services have multiple implementations, and you need to inject them dynamically without changing the client code.

### **Example: Notification System using Bridge in Spring Boot**

#### 1. Create the **Implementor Interface** (NotificationSender)
```java
public interface NotificationSender {
    void send(String message);
}
```

#### 2. Implement Concrete Implementors (Email and SMS)
```java
@Component
public class EmailSender implements NotificationSender {
    @Override
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}

@Component
public class SmsSender implements NotificationSender {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}
```

#### 3. Create the **Abstraction** (Notification)
```java
public abstract class Notification {
    protected NotificationSender sender;

    public Notification(NotificationSender sender) {
        this.sender = sender;
    }

    public abstract void notify(String message);
}
```

#### 4. Create **Refined Abstraction** (UrgentNotification)
```java
@Component
public class UrgentNotification extends Notification {

    @Autowired
    public UrgentNotification(NotificationSender sender) {
        super(sender);
    }

    @Override
    public void notify(String message) {
        System.out.println("Urgent Notification:");
        sender.send(message);
    }
}
```

#### 5. Use the Bridge Pattern in a Controller
```java
@RestController
@RequestMapping("/notifications")
public class NotificationController {

    private final UrgentNotification notification;

    @Autowired
    public NotificationController(UrgentNotification notification) {
        this.notification = notification;
    }

    @PostMapping("/send")
    public String sendNotification(@RequestBody String message) {
        notification.notify(message);
        return "Notification sent!";
    }
}
```

In this example, the **Bridge Pattern** allows you to switch between different ways of sending notifications (email or SMS) without changing the client code (the `NotificationController`).

---

### **Advantages of the Bridge Pattern**

1. **Improved Flexibility:** Abstraction and implementation can vary independently.
2. **Reduced Code Duplication:** Avoids combinatorial explosion of subclasses.
3. **Easier to Extend:** You can add new implementations or abstractions without affecting existing code.

### **Disadvantages of the Bridge Pattern**

1. **Increased Complexity:** Separating abstraction from implementation can introduce additional complexity.
2. **More Boilerplate:** You might end up writing more code to implement abstractions and concrete implementations.

---

### **Summary**

The **Bridge Pattern** is an essential design pattern to consider when your class hierarchy is growing unmanageable due to multiple dimensions of variations. In **Spring Boot**, this pattern can help in building flexible and scalable systems by dynamically injecting different implementations of a service. Use this pattern when you need to decouple abstraction from implementation and allow them to evolve independently, but avoid it when simpler solutions can suffice.

---

### Reference Links

1. https://refactoring.guru/design-patterns/bridge

---