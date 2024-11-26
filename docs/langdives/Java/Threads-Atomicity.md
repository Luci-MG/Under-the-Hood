# **Atomicity**

Atomicity is a fundamental concept in multithreading and concurrency that ensures operations are executed entirely or not at all, with no intermediate states visible to other threads. In Java, atomicity plays a crucial role in maintaining data consistency in concurrent environments.

This Article covers everything about atomic operations, issues with atomicity, atomic classes in Java, and best practices to ensure atomic behavior in your code.

---

## **What is Atomicity ?**

In a multithreaded program, atomicity guarantees that operations are executed as a single, indivisible unit. When an operation is atomic, it ensures that:

- No other thread can see the intermediate state of the operation.
- The operation either completes fully or fails without any effect.

---

## **Why it is Important ?**

Without atomic operations, multiple threads could **interfere** with each other, leading to **race conditions** and **data inconsistencies**. For example, if two threads try to **increment a shared counter** simultaneously, the result may not reflect both increments due to **interleaving** of operations.

---

## **Problems ?**

???+ example "Non-Atomic Operations on Primitive Data Types"
    ```java title="Counter Increment Example"
    class Counter {
        private int count = 0;

        public void increment() {
            count++;  // Not atomic
        }

        public int getCount() {
            return count;
        }
    }
    ```

    !!! danger "Problem"
        The statement `count++` is **not atomic**. It consists of **three operations**

        - **Read** the value of `count`.
        - **Increment** the value.
        - **Write** the new value back to `count`.

    If two threads execute `count++` simultaneously, **one increment might be lost** due to race conditions.

---

## **How to Ensure Atomicity ?**

Java provides several ways to ensure **atomicity**, including:

- **`synchronized` blocks and methods**.
- **Explicit locks** using `ReentrantLock`.
- **Atomic classes** from the `java.util.concurrent.atomic` package (recommended for simple atomic operations).

---

## **Java’s `Atomic` Classes**

The **`java.util.concurrent.atomic`** package offers classes that support lock-free, thread-safe operations on single variables. These classes rely on low-level atomic operations (like CAS — Compare-And-Swap) provided by the underlying hardware.

### **Common Atomic Classes**
- **`AtomicInteger`** – Atomic operations on integers.
- **`AtomicLong`** – Atomic operations on long values.
- **`AtomicBoolean`** – Atomic operations on boolean values.
- **`AtomicReference<V>`** – Atomic operations on reference variables.
- **`AtomicStampedReference<V>`** – Supports versioned references to prevent ABA problems.

### **`AtomicInteger`**

???+ example "Example: Solving the Increment Problem"
    ```java
    import java.util.concurrent.atomic.AtomicInteger;

    class AtomicCounter {
        private final AtomicInteger count = new AtomicInteger(0);

        public void increment() {
            count.incrementAndGet();  // Atomic increment
        }

        public int getCount() {
            return count.get();
        }
    }

    public class Main {
        public static void main(String[] args) throws InterruptedException {
            AtomicCounter counter = new AtomicCounter();

            Thread t1 = new Thread(() -> {
                for (int i = 0; i < 1000; i++) {
                    counter.increment();
                }
            });

            Thread t2 = new Thread(() -> {
                for (int i = 0; i < 1000; i++) {
                    counter.increment();
                }
            });

            t1.start();
            t2.start();
            t1.join();
            t2.join();

            System.out.println("Final Count: " + counter.getCount());  // Output: 2000
        }
    }
    ```

    ???+ info "Explanation"
        - The `incrementAndGet()` method ensures **atomicity** without using locks.
        - This solution is **faster** and **more scalable** than using `synchronized` or `ReentrantLock`.


### **`AtomicBoolean`**

???+ example "Example: Managing Flags Safely"
    ```java
    import java.util.concurrent.atomic.AtomicBoolean;

    class FlagManager {
        private final AtomicBoolean isActive = new AtomicBoolean(false);

        public void activate() {
            if (isActive.compareAndSet(false, true)) {
                System.out.println("Flag activated.");
            } else {
                System.out.println("Flag already active.");
            }
        }

        public void deactivate() {
            if (isActive.compareAndSet(true, false)) {
                System.out.println("Flag deactivated.");
            } else {
                System.out.println("Flag already inactive.");
            }
        }
    }

    public class Main {
        public static void main(String[] args) {
            FlagManager manager = new FlagManager();

            Thread t1 = new Thread(manager::activate);
            Thread t2 = new Thread(manager::activate);

            t1.start();
            t2.start();
        }
    }
    ```

    ???+ info "Explanation"
        `compareAndSet()` changes the flag only if it matches the expected value, ensuring thread safety.


### **`AtomicReference`**

???+ example "Example: Atomic Operations on Objects"
    ```java 
    import java.util.concurrent.atomic.AtomicReference;

    class Person {
        String name;

        Person(String name) {
            this.name = name;
        }
    }

    public class AtomicReferenceExample {
        public static void main(String[] args) {
            AtomicReference<Person> personRef = new AtomicReference<>(new Person("Alice"));

            // Atomic update of the reference
            personRef.set(new Person("Bob"));
            System.out.println("Updated Person: " + personRef.get().name);
        }
    }
    ```

!!! note "When to Use ?"
    Use `AtomicReference` when you need atomic operations on object references.


### **`AtomicStampedReference`**

The ABA problem occurs when a value changes from `A` to `B` and then back to `A`. `AtomicStampedReference` solves this by associating a version (stamp) with the value.

???+ example "Example: ABA problem prevention"
    ```java
    import java.util.concurrent.atomic.AtomicStampedReference;

    public class AtomicStampedReferenceExample {
        public static void main(String[] args) {
            AtomicStampedReference<Integer> ref = new AtomicStampedReference<>(1, 0);

            int[] stamp = new int[1];
            Integer value = ref.get(stamp);
            System.out.println("Initial Value: " + value + ", Stamp: " + stamp[0]);

            boolean success = ref.compareAndSet(1, 2, stamp[0], stamp[0] + 1);
            System.out.println("CAS Success: " + success + ", New Value: " + ref.get(stamp) + ", New Stamp: " + stamp[0]);
        }
    }
    ```

    ???+ info "Explanation"
        `AtomicStampedReference` ensures that the same value change does not go undetected by tracking the version.

---

## **Performance ?**

- **No Locks**: Atomic operations are **non-blocking** and do not require heavy locks, improving **throughput**.
- **Scalability**: They perform well in **highly concurrent environments**.
- **Low Overhead**: CAS operations are supported natively by hardware, providing low-latency operations.

---

## **When to Use ?**

- Use `AtomicInteger` or `AtomicBoolean` instead of `synchronized` methods like simple counters or flags.

- Use `AtomicReference` for lock-free algorithms (Non blocking Algos).

- Use `AtomicStampedReference` for versioned updates to prevent ABA problems.

---

## **Limitations ?**

- Atomic classes only work with **single variables**. For **multiple variables**, use `synchronized` or `ReentrantLock`.

- For **complex operations** involving multiple state changes, **atomic classes** are insufficient.

- Atomic operations avoid deadlocks but can still suffer from **livelocks** (threads continuously retrying without progress).

---

## **Best Practices**

- Use atomic classes for simple state management (e.g., counters, flags).
- Avoid overuse of atomic classes for complex operations, use `synchronized` or `ReentrantLock`.
- Monitor performance while atomic operations are faster, they might not suit every situation (e.g., write-heavy workloads).
- Avoid busy-waiting with atomic classes to prevent CPU wastage.

---

## **Summary**

The atomic classes in Java’s `java.util.concurrent.atomic` package offer lock-free, thread-safe operations that are ideal for simple state management. By ensuring atomicity, these classes help avoid race conditions and improve the performance and scalability of multithreaded applications. However, they are best suited for single-variable updates for more complex operations, locks or transactional mechanisms may still be necessary.

---