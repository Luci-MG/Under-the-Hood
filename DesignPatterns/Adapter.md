# **Adapter Design Pattern**

The **Adapter Pattern** is a structural design pattern in software development that allows objects with incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces, providing a wrapper or a mediator to enable their interaction without changing their existing code.

---

### **1. What is the Adapter Pattern?**
The **Adapter Pattern** converts the interface of a class into another interface that a client expects. This helps integrate two systems with different interfaces so they can work together without altering their code. It is often used when a legacy system needs to be integrated with new components or when third-party APIs are integrated into an existing codebase.

### **Analogy**:
- Think of a power plug adapter: You have an appliance with a US plug (two flat pins), but you need to connect it to a European socket (two round holes). The adapter ensures that both the incompatible interfaces (US and European plugs) work together without modifying either.

---

### **2. Ways to Create Adapter Pattern**

There are **two common ways** to implement the Adapter Pattern:

1. **Class Adapter** (Inheritance-based)
2. **Object Adapter** (Composition-based)

#### **2.1. Class Adapter Pattern (via Inheritance)**

In this approach, the adapter class **extends** the adaptee (the class that has the incompatible interface) and **implements the interface** that the client expects.

#### **Example Code (Java) - Class Adapter**

```java
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

##### **Explanation:**
- `MediaAdapter` extends `AdvancedMediaPlayer` (inheriting the original functionality) and implements the `MediaPlayer` interface (adapting it to what the client expects).


#### **2.2. Object Adapter Pattern (via Composition)**

In this approach, the adapter **contains an instance** of the adaptee class and delegates calls to the appropriate methods.

##### **Example Code (Java) - Object Adapter**

```java
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

##### **Explanation:**
- In this version, `MediaAdapter` holds a reference to the `AdvancedMediaPlayer` instance and **delegates** method calls instead of extending the class.

---

### **3. When to Use the Adapter Pattern?**
- **When two interfaces are incompatible** but need to work together.
- **When a legacy system** needs to integrate with new components.
- **When using third-party libraries or APIs** that do not conform to your existing codebase’s interfaces.
- **When you want to reuse an existing class**, but its interface is not compatible with the rest of your system.

---

### **4. When Not to Use the Adapter Pattern?**
- When you **have control over the original code** (you can modify the existing class instead of creating an adapter).
- If using the Adapter Pattern makes your design **too complex** (consider refactoring if the incompatibility is minimal).
- If the Adapter adds **unnecessary overhead** in performance-sensitive environments.

---

### **5. Adapter Pattern with Spring Boot Example**

In a **Spring Boot** context, the Adapter Pattern can be used to integrate an external or legacy service with your application's service layer.

##### **Example: Integrating a Legacy Payment Service in Spring Boot**

```java
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

##### **Explanation:**
1. `LegacyPaymentService` is an old service with an incompatible method.
2. `PaymentServiceAdapter` acts as an adapter by implementing the `PaymentService` interface and internally calling the legacy service.
3. The `PaymentController` depends on `PaymentService`, which can now work seamlessly with the legacy system.

---

### **6. Summary - Why Use the Adapter Pattern?**
- **Decouples** client code from specific implementations, enhancing flexibility.
- **Promotes reusability** by allowing incompatible systems to work together without modification.
- **Improves maintainability** by isolating legacy or third-party code in adapters.
- **Simplifies testing**, as adapters can be mocked or stubbed easily.

---