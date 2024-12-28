[//]: # (TODO:: Need to add examples in other Language )

# **Facade**

The Facade Pattern is a structural design pattern commonly used to provide a simple, unified interface to a complex subsystem of classes, libraries, or frameworks. This pattern makes a complex library or system easier to use by hiding the underlying complexities and exposing only the functionality that is relevant for the client.


## **What ?**

- The Facade Pattern defines a higher-level interface that makes a system easier to use by hiding its complexity.
- It provides a single interface or entry point to a group of related classes or modules.
- It simplifies interactions between the client and complex subsystems.
  

## **How to Create ?**

- Identify a complex subsystem that needs simplification.
- Design a Facade class to serve as the entry point for interacting with the subsystem.
- Expose only the essential methods in the Facade class that clients need.
- Delegate requests from the Facade class to the appropriate subsystem components.
- Hide the internal complexity of subsystems from the client.


## **When to Use ?**

- When you need to provide a simpler interface to a complex subsystem.
- When you want to minimize the dependency of your client code on the inner details of your system.
- When you make changes to the subsystems won’t impact the client code since the facade hides those details.
- When you have multiple clients that need to interact with a system in a unified way.


## **Where Not to Use ?**

- If the system is already simple No need to add an extra abstraction layer.
- When full access to the subsystem is required If clients need to use the complete functionality of the subsystem, a facade might limit access.
- When high performance is critical If adding the facade introduces unnecessary overhead.
- Too many Facades can be anti-pattern overusing facades may cause code to become harder to maintain by hiding too much behind layers of abstraction.


## **Advantages**

- Simplifies code usage Reduces the number of classes exposed to the client.
- Encapsulates complexity Hides the intricacies of the subsystems.
- Improves maintainability Changes in subsystems don’t affect the client directly.
- Reduces dependencies Decouples the client from internal subsystems.


## **Disadvantages**

- Over-abstraction Might hide too much, making it hard to access specific functionality.
- Performance overhead If not designed well, adding a facade might introduce latency.
- Risk of becoming an anti-pattern Overuse can lead to poorly maintainable code due to excessive abstraction layers.


## **How to Apply ?**

??? example "Simple Example"

    Let's go through a simple Java example demonstrating a Facade pattern for a Home Theater System:

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

    ``` title="Output"
    Setting up movie...
    Amplifier is ON.
    Projector is ON.
    Playing movie.
    Shutting down movie...
    Stopping movie.
    Projector is OFF.
    Amplifier is OFF.
    ```

    ???+ info "Explanation:"
        - Subsystem classes (Amplifier, DVDPlayer, Projector) have their own logic.
        - Facade class (HomeTheaterFacade) abstracts the interactions between these subsystems and provides a simple interface for the client.
        - The client only needs to call the watchMovie() and endMovie() methods without worrying about the complex setup logic.


??? example "Spring Boot Example"

    In Spring Boot, the Facade pattern can be applied to services or controllers to hide the complexity of business logic or external systems. For example, a facade class can wrap multiple service calls or integrate external APIs to provide a simplified interface to the client (like a REST controller).

    Let's go through a example of how to apply the Facade pattern in a Spring Boot application.

    **Example Scenario**: A Payment System interacts with several services (like PaymentGatewayService, NotificationService, and OrderService). We create a PaymentFacade to simplify the interaction.

    ```java title="Step-1: Subsystem Services"
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

    ```java title="Step-2: Facade Class"
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

    ```java title="Step-3: Controller"
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

    ???+ info "Explanation"
        - PaymentFacade: Acts as the Facade, simplifying the interactions between `PaymentGatewayService`, `OrderService`, and `NotificationService`.
        - Controller: Uses the `PaymentFacade` to expose a single endpoint for making payments.
        - This ensures that the controller does not need to handle complex business logic and can focus only on handling HTTP requests.


## **Summary**

The Facade Pattern is a powerful tool for simplifying interactions with complex systems. It is especially useful when working with large subsystems or external APIs, as it encapsulates the internal workings and provides a simple interface to clients. In a Spring Boot application, you can use it to manage complex business logic or interactions with multiple services within a single, cohesive facade. However, it should be used judiciously to avoid over-abstraction or unnecessary complexity.

!!! note 
    #### This pattern is best used when:
        - You need to simplify client interactions.
        - You want to decouple the client from a complex subsystem.
        - You aim to improve maintainability and reduce dependencies.

    #### Avoid using it when:
        - The system is already simple.
        - Performance is a key concern.

This makes the Facade pattern a valuable asset in both object-oriented design and modern frameworks like Spring Boot.

---