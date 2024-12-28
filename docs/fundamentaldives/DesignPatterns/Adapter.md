[//]: # (TODO:: Need to add examples in other Language )

# **Adapter Design Pattern**

## **What ?**


The Adapter Pattern is a structural design pattern in software development that allows objects with incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces, providing a wrapper or a mediator to enable their interaction without changing their existing code.

This Pattern converts the interface of a class into another interface that a client expects. This helps integrate two systems with different interfaces so they can work together without altering their code. It is often used when a legacy system needs to be integrated with new components or when third-party APIs are integrated into an existing codebase.

!!! info "Analogy"
    Think of a power plug adapter You have an appliance with a US plug (two flat pins), but you need to connect it to a European socket (two round holes). The adapter ensures that both the incompatible interfaces (US and European plugs) work together without modifying either.


## **When to Use ?**
- When two interfaces are incompatible but need to work together.
- When a legacy system needs to integrate with new components.
- When using third-party libraries or APIs that do not conform to your existing codebase’s interfaces.
- When you want to reuse an existing class, but its interface is not compatible.


## **When Not to Use ?**
- When you have control over the original code (you can modify the existing class).
- If using the Adapter Pattern makes your design too complex (consider refactoring).
- If the Adapter adds unnecessary overhead in performance-sensitive environments.


## **Ways to Implement**

???+ example "Simple Example"

    There are two common ways to implement the Adapter Pattern:

    1. Class Adapter (Inheritance-based)
    2. Object Adapter (Composition-based)

    ??? example "Class Adapter Pattern (via Inheritance)"

        In this approach, the adapter class extends the adaptee (the class that has the incompatible interface) and implements the interface that the client expects.

        ```java title="Class Adapter Java Example"
        // Target Interface - The desired interface that client expects
        interface MediaPlayer {
            void play(String audioType, String fileName);
        }

        // Adaptee - Incompatible interface that needs adaptation
        class AdvancedMediaPlayer {
            void playMp3(String fileName) {
                System.out.println("Playing mp3 file: " + fileName);
            }

            void playMp4(String fileName) {
                System.out.println("Playing mp4 file: " + fileName);
            }
        }

        // Class Adapter - Adapts AdvancedMediaPlayer to MediaPlayer
        class MediaAdapter extends AdvancedMediaPlayer implements MediaPlayer {
            @Override
            public void play(String audioType, String fileName) {
                if (audioType.equalsIgnoreCase("mp3")) {
                    playMp3(fileName);
                } else if (audioType.equalsIgnoreCase("mp4")) {
                    playMp4(fileName);
                }
            }
        }

        // Client Code
        public class AudioPlayer {
            public static void main(String[] args) {
                MediaPlayer player = new MediaAdapter();
                player.play("mp3", "song.mp3");
                player.play("mp4", "video.mp4");
            }
        }
        ```

        ???+ info "Explanation"
            `MediaAdapter` extends `AdvancedMediaPlayer` (inheriting the original functionality) and implements the `MediaPlayer` interface (adapting it to what the client expects).


    ??? example "Object Adapter Pattern (via Composition)"

        In this approach, the adapter contains an instance of the adaptee class and delegates calls to the appropriate methods.

        ```java title="Object Adapter Java Example"
        // Target Interface
        interface MediaPlayer {
            void play(String audioType, String fileName);
        }

        // Adaptee
        class AdvancedMediaPlayer {
            void playMp3(String fileName) {
                System.out.println("Playing mp3 file: " + fileName);
            }

            void playMp4(String fileName) {
                System.out.println("Playing mp4 file: " + fileName);
            }
        }

        // Object Adapter
        class MediaAdapter implements MediaPlayer {
            private AdvancedMediaPlayer advancedPlayer;

            public MediaAdapter(AdvancedMediaPlayer advancedPlayer) {
                this.advancedPlayer = advancedPlayer;
            }

            @Override
            public void play(String audioType, String fileName) {
                if (audioType.equalsIgnoreCase("mp3")) {
                    advancedPlayer.playMp3(fileName);
                } else if (audioType.equalsIgnoreCase("mp4")) {
                    advancedPlayer.playMp4(fileName);
                }
            }
        }

        // Client Code
        public class AudioPlayer {
            public static void main(String[] args) {
                AdvancedMediaPlayer advancedPlayer = new AdvancedMediaPlayer();
                MediaPlayer adapter = new MediaAdapter(advancedPlayer);
                adapter.play("mp3", "song.mp3");
                adapter.play("mp4", "video.mp4");
            }
        }
        ```

        ???+ info "Explanation"
            In this version, `MediaAdapter` holds a reference to the `AdvancedMediaPlayer` instance and **delegates** method calls instead of extending the class.


??? example "Spring Boot Example"

    In a Spring Boot context, the Adapter Pattern can be used to integrate an external or legacy service with your application's service layer.

    ```java title="Integrating a Legacy Payment Service"
    // Legacy Payment Service - Adaptee
    class LegacyPaymentService {
        public void payWithCreditCard(String cardNumber) {
            System.out.println("Payment made using Legacy Credit Card: " + cardNumber);
        }
    }

    // Target Interface
    interface PaymentService {
        void processPayment(String cardNumber);
    }

    // Adapter Implementation - Integrating LegacyPaymentService with PaymentService
    @Component
    class PaymentServiceAdapter implements PaymentService {
        private final LegacyPaymentService legacyService;

        // Constructor injection
        public PaymentServiceAdapter(LegacyPaymentService legacyService) {
            this.legacyService = legacyService;
        }

        @Override
        public void processPayment(String cardNumber) {
            legacyService.payWithCreditCard(cardNumber);
        }
    }

    // Spring Boot Controller
    @RestController
    @RequestMapping("/payments")
    public class PaymentController {

        private final PaymentService paymentService;

        @Autowired
        public PaymentController(PaymentService paymentService) {
            this.paymentService = paymentService;
        }

        @PostMapping
        public String makePayment(@RequestParam String cardNumber) {
            paymentService.processPayment(cardNumber);
            return "Payment Successful";
        }
    }
    ```

    ???+ info "Explanation"
        - `LegacyPaymentService` is an old service with an incompatible method.
        - `PaymentServiceAdapter` acts as an adapter by implementing the `PaymentService` interface and internally calling the legacy service.
        - The `PaymentController` depends on `PaymentService`, which can now work seamlessly with the legacy system.


## **Summary**

The Adapter Pattern enhances flexibility by decoupling client code from specific implementations, promotes reusability by enabling compatibility between systems, improves maintainability by isolating legacy or third-party code, and simplifies testing through easy mock or stub usage.

---