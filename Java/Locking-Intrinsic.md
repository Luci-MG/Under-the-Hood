# Locking.

Locking is an essential concept in multithreaded programming to prevent race conditions and ensure thread safety. When multiple threads access shared resources, locks ensure that only one thread accesses the critical section at a time.

This deep dive covers synchronized blocks, reentrant locks, read-write locks, deadlock scenarios, atomic classes, and modern Java utilities — everything related to locking in Java.

---

## 1. **What is Locking?**

- **Locking** is a way to ensure that **only one thread at a time** executes a critical section or modifies a shared resource.
- Without proper locks, multiple threads may interfere with each other, leading to **data inconsistency** or **unexpected behavior** (race conditions).

Java offers various **locking mechanisms**, from **synchronized blocks** to **explicit locks** like `ReentrantLock`.

---

## 2. **Synchronized Keyword and Intrinsic Locks**

Java’s **`synchronized`** keyword is one of the primary ways to control **access to shared resources** in multithreaded programs. It ensures **thread safety** by providing **mutual exclusion** and **visibility guarantees**. Let's go further into every aspect of `synchronized`.

### **How `synchronized` Works Internally**

When a method or block is marked as `synchronized`, the **JVM uses a monitor lock (intrinsic lock)** for the associated object or class. The **monitor** is a synchronization construct provided by the JVM.

**Two things happen when a thread enters a synchronized block or method:**
1. **Mutual Exclusion:** No other thread can enter the synchronized block on the same object until the first thread exits.
2. **Visibility Guarantee:** Changes made by the thread to variables inside the synchronized block are **visible to other threads**.

**Intrinsic Lock:**
- Each **Java object has an intrinsic lock** (also called **monitor lock**) associated with it.
- The thread that enters the synchronized block **acquires the intrinsic lock**. When it leaves the block, it **releases the lock**, allowing other threads to acquire it.

---

## **Synchronized Methods: Instance-Level and Class-Level Locks**

### 1. **Instance-Level Locking**

When you synchronize a **non-static method**, the thread **acquires the lock on the instance** of the class (the **`this` object**).

```java
public synchronized void increment() {
    // Lock acquired on the current instance (this)
    count++;
}
```

- **Impact**: If multiple threads try to access `increment()` on the **same object instance**, only one thread will execute the method at a time. 
- **Different Instances:** If threads are working on **different instances** of the class, they can run synchronized methods simultaneously.

### **Example with Instance-Level Locking**

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
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

- **Why does this work?** Since both threads are operating on the same `Counter` object, **only one thread at a time** can execute the `increment()` method due to **instance-level locking**.

---

### 2. **Class-Level Locking**

A **static synchronized method** locks on the **Class object** (i.e., `ClassName.class`) rather than on an instance. This ensures that all threads calling static methods on the class are synchronized.

```java
public synchronized static void staticIncrement() {
    // Lock acquired on the class object (Counter.class)
}
```

- **Impact**: If a thread calls a **static synchronized method**, no other thread can access any **other static synchronized method** on that class until the lock is released. However, threads can still call non-static synchronized methods, since **the instance and class-level locks are different**.

### **Example with Class-Level Locking**

```java
class Counter {
    private static int count = 0;

    public synchronized static void increment() {
        count++;
    }

    public synchronized static int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) Counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) Counter.increment();
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();

        System.out.println("Final Count: " + Counter.getCount());  // Output: 2000
    }
}
```

---

## **Synchronized Blocks: Fine-Grained Control**

A **synchronized block** provides **more control** than a synchronized method. You can choose **which object’s intrinsic lock** to use, instead of locking the entire method.

```java
public void increment() {
    synchronized (this) {  // Locking on the current instance
        count++;
    }
}
```

### **When to Use Synchronized Blocks**
- **To improve performance**: Instead of synchronizing the entire method, only the critical section needs to be synchronized.
- **To lock on specific objects**: You can use any object as the lock, not just the instance or class.

```java
class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {  // Locking on a custom object
            count++;
        }
    }
}
```

### **Example: Synchronized Block with Fine-Grained Control**

```java
public void updateBothCounters(Counter counter1, Counter counter2) {
    synchronized (counter1) {  // Locking on the first Counter object
        counter1.increment();
    }
    synchronized (counter2) {  // Locking on the second Counter object
        counter2.increment();
    }
}
```

---

## **How Intrinsic Locks Work Internally (JVM Implementation)**

1. **Entry to the Monitor:**
   - When a thread enters a synchronized block or method, it **acquires the monitor** on the object or class.
   - If another thread tries to enter the same block, it **gets blocked** until the monitor is released.

2. **Exit from the Monitor:**
   - When the thread exits the synchronized block, the monitor is **released**, and another thread waiting on the monitor can proceed.

3. **Bias Locking and Lightweight Locking:**
   - The JVM optimizes locks with **biased locking** (when only one thread uses the lock most of the time) and **lightweight locking** (fast path for uncontended locks).
   - If **contention occurs**, the JVM escalates the lock to a **heavyweight lock** (blocking other threads).

---

## **Best Practices for Using Synchronized**

1. **Use synchronized only where necessary**: Synchronizing unnecessary code slows down the application.

2. **Minimize the scope of synchronization**: Use **synchronized blocks** rather than whole methods to **reduce contention**.

3. **Use synchronized on the correct objects**: Ensure you **synchronize on the same object** across threads to avoid incorrect locking behavior.

4. **Avoid nested locks** (Deadlock risk): Nested synchronized blocks can lead to **deadlock**. Use consistent lock ordering.

---

## **Potential Issues with Synchronized**

1. **Deadlock**: Occurs if two or more threads are waiting for each other to release locks.

2. **Performance Bottlenecks**: Overusing synchronization can lead to **contention**, where threads are constantly waiting to acquire locks.

3. **Livelock**: Threads keep responding to each other without making progress (e.g., both threads keep yielding the lock to each other).

---