# Locking.

Locking is an essential concept in multithreaded programming to prevent race conditions and ensure thread safety. When multiple threads access shared resources, locks ensure that only one thread accesses the critical section at a time.

This deep dive covers synchronized blocks, reentrant locks, read-write locks, deadlock scenarios, atomic classes, and modern Java utilities — everything related to locking in Java.

---

## 1. **What is Locking?**

- **Locking** is a way to ensure that **only one thread at a time** executes a critical section or modifies a shared resource.
- Without proper locks, multiple threads may interfere with each other, leading to **data inconsistency** or **unexpected behavior** (race conditions).

Java offers various **locking mechanisms**, from **synchronized blocks** to **explicit locks** like `ReentrantLock`.

---

## **2. What is `ReentrantLock`?**

The **`ReentrantLock`** class, introduced in **Java 5**, offers more control over thread synchronization than the `synchronized` keyword. It allows for advanced locking techniques such as **fairness policies**, **tryLock**, and **interruptible locks**. Let’s explore everything about `ReentrantLock`, including its use cases, internal mechanisms, and best practices.

`ReentrantLock` is a **concrete class** in the `java.util.concurrent.locks` package that implements the **Lock interface**. It provides:
- **Fine-grained control over locking**, including **fair and unfair locks**.
- The ability for a thread to **re-acquire a lock it already holds** without blocking (hence the term **"reentrant"**).
- **Explicit unlocking**, unlike the `synchronized` keyword, which automatically releases the lock when the block exits.

---

## **4. How `ReentrantLock` Works Internally**

1. **Lock Acquisition:**
   - When a thread calls `lock()`, it tries to acquire the lock. If the lock is available, the thread proceeds; otherwise, it **blocks** until the lock becomes available.

2. **Reentrancy:**
   - A thread that holds the lock can **acquire the lock again** without blocking. This is useful when a thread **enters a method that also calls another synchronized method** or block that requires the same lock.

3. **Fair vs Unfair Locking:**
   - **Fair Lock**: Threads are granted access **in the order they requested the lock**.
   - **Unfair Lock**: Threads may **skip the queue** if the lock is released, improving performance but reducing fairness.

---

## **5. Basic Example of `ReentrantLock`**

```java
import java.util.concurrent.locks.ReentrantLock;

class Counter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock();  // Acquire the lock
        try {
            count++;
        } finally {
            lock.unlock();  // Release the lock
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();

        System.out.println("Final Count: " + counter.getCount());  // Output: 2000
    }
}
```

---

## **6. Fair vs Unfair Lock in ReentrantLock**

### **Fair Lock Example**
A **fair lock** ensures that the **longest-waiting thread** gets the lock first.

```java
ReentrantLock lock = new ReentrantLock(true);  // Fair lock
```

- **Advantage**: Avoids **thread starvation**.
- **Disadvantage**: May have **lower performance** due to increased overhead.

### **Unfair Lock Example (Default Behavior)**

```java
ReentrantLock lock = new ReentrantLock();  // Unfair lock (default)
```

- **Advantage**: Better **throughput** because threads are allowed to "jump the queue".
- **Disadvantage**: Can lead to **thread starvation**, where some threads may not get a chance to execute.

---

## **7. Advanced Locking Techniques with `ReentrantLock`**

### **tryLock(): Non-Blocking Lock Acquisition**

The `tryLock()` method attempts to acquire the lock **without blocking**. It returns **true** if the lock is acquired, otherwise **false**.

```java
if (lock.tryLock()) {
    try {
        // Perform task
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Could not acquire lock, doing something else...");
}
```

- **Use Case**: When you want to avoid **blocking indefinitely** if the lock is not available.

---

### **tryLock with Timeout**

The `tryLock(long timeout, TimeUnit unit)` method waits for a specific amount of time to acquire the lock.

```java
import java.util.concurrent.TimeUnit;

if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        // Perform task
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Could not acquire lock within timeout.");
}
```

- **Use Case**: When waiting indefinitely is not practical, such as **network operations** or **I/O tasks**.

---

### **Interruptible Lock Acquisition**

The `lockInterruptibly()` method allows a thread to acquire the lock but **respond to interrupts** while waiting.

```java
try {
    lock.lockInterruptibly();  // Wait for lock, but respond to interrupts
    try {
        // Perform task
    } finally {
        lock.unlock();
    }
} catch (InterruptedException e) {
    System.out.println("Thread was interrupted.");
}
```

- **Use Case**: Use when a thread needs to be **interrupted** while waiting for a lock.

---

## **8. Reentrant Lock Behavior**

A **reentrant lock** means that the same thread can **acquire the lock multiple times** without blocking itself. However, the thread must **release the lock the same number of times** to fully unlock it.

### **Reentrant Behavior Example**

```java
class ReentrantExample {
    private final ReentrantLock lock = new ReentrantLock();

    public void outerMethod() {
        lock.lock();
        try {
            System.out.println("In outer method");
            innerMethod();
        } finally {
            lock.unlock();
        }
    }

    public void innerMethod() {
        lock.lock();
        try {
            System.out.println("In inner method");
        } finally {
            lock.unlock();
        }
    }
}
```

- In this example, **`outerMethod`** calls **`innerMethod`**, and both methods acquire the same lock. This works without issues because `ReentrantLock` allows **reentrant locking**.

---

## **9. Condition Variables with ReentrantLock**

The `Condition` interface (associated with a `ReentrantLock`) allows a thread to **wait for a condition to be met** before proceeding. It provides **better control** than the traditional `wait()`/`notify()`.

### **Example of Condition Variables**

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

class ConditionExample {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean ready = false;

    public void awaitCondition() throws InterruptedException {
        lock.lock();
        try {
            while (!ready) {
                condition.await();  // Wait for signal
            }
            System.out.println("Condition met, proceeding...");
        } finally {
            lock.unlock();
        }
    }

    public void signalCondition() {
        lock.lock();
        try {
            ready = true;
            condition.signal();  // Signal waiting thread
        } finally {
            lock.unlock();
        }
    }
}
```

---

## **10. Performance Considerations**

- **ReentrantLock** has more overhead than `synchronized` due to **fairness policies** and **explicit lock management**.
- **Use `synchronized` for simple scenarios**; use **ReentrantLock** for more **complex locking requirements** (e.g., tryLock, fairness).