# **Facade Design Pattern**

The **Facade Pattern** is a structural design pattern commonly used to provide a simple, unified interface to a complex subsystem of classes, libraries, or frameworks. This pattern makes a complex library or system easier to use by hiding the underlying complexities and exposing only the functionality that is relevant for the client.

---

### **What is Facade Pattern?**

- The **Facade Pattern** defines a higher-level interface that makes a system easier to use by hiding its complexity.
- It provides a **single interface** or **entry point** to a group of related classes or modules.
- It simplifies interactions between the client and complex subsystems.
  
---

### **How to Create a Facade Pattern?**

1. **Identify a complex subsystem** that needs simplification.
2. **Design a Facade class** to serve as the entry point for interacting with the subsystem.
3. **Expose only the essential methods** in the Facade class that clients need.
4. **Delegate requests from the Facade class** to the appropriate subsystem components.
5. **Hide the internal complexity** of subsystems from the client.

---

### **Code Example: Basic Facade Pattern in Java**

Below is a simple Java example demonstrating a Facade pattern for a **Home Theater System**:

```java
// Subsystem classes
class Amplifier {
    public void on() { System.out.println("Amplifier is ON."); }
    public void off() { System.out.println("Amplifier is OFF."); }
}

class DVDPlayer {
    public void play() { System.out.println("Playing movie."); }
    public void stop() { System.out.println("Stopping movie."); }
}

class Projector {
    public void on() { System.out.println("Projector is ON."); }
    public void off() { System.out.println("Projector is OFF."); }
}

// Facade class
class HomeTheaterFacade {
    private Amplifier amplifier;
    private DVDPlayer dvdPlayer;
    private Projector projector;

    public HomeTheaterFacade(Amplifier amp, DVDPlayer dvd, Projector proj) {
        this.amplifier = amp;
        this.dvdPlayer = dvd;
        this.projector = proj;
    }

    public void watchMovie() {
        System.out.println("Setting up movie...");
        amplifier.on();
        projector.on();
        dvdPlayer.play();
    }

    public void endMovie() {
        System.out.println("Shutting down movie...");
        dvdPlayer.stop();
        projector.off();
        amplifier.off();
    }
}

// Client code
public class FacadePatternDemo {
    public static void main(String[] args) {
        Amplifier amp = new Amplifier();
        DVDPlayer dvd = new DVDPlayer();
        Projector proj = new Projector();

        HomeTheaterFacade homeTheater = new HomeTheaterFacade(amp, dvd, proj);

        homeTheater.watchMovie();
        homeTheater.endMovie();
    }
}
```

#### **Output:**
```
Setting up movie...
Amplifier is ON.
Projector is ON.
Playing movie.
Shutting down movie...
Stopping movie.
Projector is OFF.
Amplifier is OFF.
```

#### **Explanation:**
- **Subsystem classes** (Amplifier, DVDPlayer, Projector) have their own logic.
- **Facade class (HomeTheaterFacade)** abstracts the interactions between these subsystems and provides a simple interface for the client.
- The client only needs to call the **watchMovie()** and **endMovie()** methods without worrying about the complex setup logic.

---

### **When to Use the Facade Pattern?**

1. **Simplify complex systems:** When you need to provide a simpler interface to a complex subsystem.
2. **Decouple client code from subsystems:** You want to minimize the dependency of your client code on the inner details of your system.
3. **Enhance code maintainability:** Changes to the subsystems won’t impact the client code since the facade hides those details.
4. **Multiple clients or user interfaces:** When you have multiple clients that need to interact with a system in a unified way.

---

### **Where Not to Use the Facade Pattern?**

1. **If the system is already simple:** No need to add an extra abstraction layer.
2. **When full access to the subsystem is required:** If clients need to use the complete functionality of the subsystem, a facade might limit access.
3. **When high performance is critical:** If adding the facade introduces unnecessary overhead.
4. **Too many Facades can be anti-pattern:** Overusing facades may cause code to become harder to maintain by hiding too much behind layers of abstraction.

---

### **How to Use Facade Pattern in Spring Boot**

In **Spring Boot**, the Facade pattern can be applied to **services or controllers** to hide the complexity of business logic or external systems. For example, a facade class can wrap multiple service calls or integrate external APIs to provide a simplified interface to the client (like a REST controller).

Here’s an example of how to apply the Facade pattern in a **Spring Boot** application.

---

#### **Example: Facade Pattern in Spring Boot**

**Scenario**: A **Payment System** interacts with several services (like PaymentGatewayService, NotificationService, and OrderService). We create a PaymentFacade to simplify the interaction.

##### Step 1: Subsystem Services

```java
@Service
public class PaymentGatewayService {
    public void processPayment(String orderId) {
        System.out.println("Processing payment for order: " + orderId);
    }
}

@Service
public class NotificationService {
    public void sendNotification(String message) {
        System.out.println("Sending notification: " + message);
    }
}

@Service
public class OrderService {
    public void completeOrder(String orderId) {
        System.out.println("Completing order: " + orderId);
    }
}
```

##### Step 2: Facade Class

```java
@Service
public class PaymentFacade {

    private final PaymentGatewayService paymentGatewayService;
    private final NotificationService notificationService;
    private final OrderService orderService;

    @Autowired
    public PaymentFacade(PaymentGatewayService paymentGatewayService,
                         NotificationService notificationService,
                         OrderService orderService) {
        this.paymentGatewayService = paymentGatewayService;
        this.notificationService = notificationService;
        this.orderService = orderService;
    }

    public void makePayment(String orderId) {
        System.out.println("Initiating payment process...");
        paymentGatewayService.processPayment(orderId);
        orderService.completeOrder(orderId);
        notificationService.sendNotification("Payment completed for order: " + orderId);
    }
}
```

##### Step 3: Controller

```java
@RestController
@RequestMapping("/api/payment")
public class PaymentController {

    private final PaymentFacade paymentFacade;

    @Autowired
    public PaymentController(PaymentFacade paymentFacade) {
        this.paymentFacade = paymentFacade;
    }

    @PostMapping("/pay/{orderId}")
    public ResponseEntity<String> pay(@PathVariable String orderId) {
        paymentFacade.makePayment(orderId);
        return ResponseEntity.ok("Payment successful for order: " + orderId);
    }
}
```

---

#### **Explanation of Spring Boot Example:**

- **PaymentFacade**: Acts as the Facade, simplifying the interactions between `PaymentGatewayService`, `OrderService`, and `NotificationService`.
- **Controller**: Uses the `PaymentFacade` to expose a single endpoint for making payments.
- This ensures that the controller does not need to handle complex business logic and can focus only on handling HTTP requests.

---

### **Advantages of Facade Pattern:**

1. **Simplifies code usage:** Reduces the number of classes exposed to the client.
2. **Encapsulates complexity:** Hides the intricacies of the subsystems.
3. **Improves maintainability:** Changes in subsystems don’t affect the client directly.
4. **Reduces dependencies:** Decouples the client from internal subsystems.

---

### **Disadvantages of Facade Pattern:**

1. **Over-abstraction:** Might hide too much, making it hard to access specific functionality.
2. **Performance overhead:** If not designed well, adding a facade might introduce latency.
3. **Risk of becoming an anti-pattern:** Overuse can lead to poorly maintainable code due to excessive abstraction layers.

---

### **Summary**

The **Facade Pattern** is a powerful tool for simplifying interactions with complex systems. It is especially useful when working with **large subsystems or external APIs**, as it encapsulates the internal workings and provides a **simple interface** to clients. In a **Spring Boot** application, you can use it to manage complex **business logic** or interactions with **multiple services** within a single, cohesive facade. However, it should be used judiciously to avoid **over-abstraction** or unnecessary complexity.

This pattern is best used when:
- You need to **simplify client interactions**.
- You want to **decouple** the client from a complex subsystem.
- You aim to improve **maintainability** and reduce **dependencies**.

Avoid using it when:
- The **system is already simple**.
- Performance is a **key concern**.

This makes the Facade pattern a valuable asset in both **object-oriented design** and **modern frameworks** like Spring Boot.

---

### Reference Links

1. https://refactoring.guru/design-patterns/facade

---