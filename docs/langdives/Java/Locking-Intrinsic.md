# **Intrinsic Locking**

Locking is an essential concept in multithreaded programming to prevent race conditions and ensure thread safety. When multiple threads access shared resources, locks ensure that only one thread accesses the critical section at a time. Java offers various locking mechanisms, from synchronized blocks to explicit locks like `ReentrantLock`.

We will cover synchronized blocks in this article.


## **Synchronized & Intrinsic Locks**

Java’s `synchronized` key word is one of the primary ways to control access to shared resources in multithreaded programs. It ensures thread safety by providing mutual exclusion and visibility guarantees. Let's go further into every aspect of `synchronized`.

### How `synchronized` Works

When a method or block is marked as `synchronized`, the JVM uses a monitor lock (intrinsic lock) for the associated object or class. The monitor is a synchronization construct provided by the JVM.

This monitor ensures two key properties:

- Mutual Exclusion: No other thread can enter the synchronized block on the same object until the first thread exits.
- Visibility Guarantee: Changes made by the thread to variables inside the synchronized block are visible to other threads.

### Intrinsic Lock

Every Java object has an intrinsic lock, also known as a monitor lock. Here's how it works internally:  

- Entry to the Monitor:  When a thread enters a synchronized block or method, it acquires the monitor associated with the object or class. If another thread attempts to enter the same synchronized block or method, it will be blocked until the current thread releases the monitor.  
   
- Exit from the Monitor:  When the thread exits the synchronized block, it releases the monitor, allowing other waiting threads to acquire it and proceed. 

### Optimizations in Locking  

The JVM employs various optimizations to reduce the overhead of synchronization:  

- Bias Locking: If a lock is consistently used by a single thread, the JVM enables biased locking to minimize the cost of acquiring the lock.  
- Lightweight Locking: For uncontended locks, the JVM uses a fast path called lightweight locking to further optimize performance.  
- Heavyweight Locking: If contention occurs (multiple threads competing for the lock), the JVM escalates to heavyweight locking, which involves blocking other threads.  

By leveraging these optimizations, the JVM ensures efficient synchronization while maintaining thread safety, even under varying levels of contention. 


## **Synchronized Methods**

### **Instance-Level Locking**

When you synchronize a non-static method, the thread acquires the lock on the instance of the class (the `this` object).

```java
public synchronized void increment() {
    // Lock acquired on the current instance (this)
    count++;
}
```

If multiple threads attempt to access the increment() method on the same object instance, only one thread will execute the method at a time due to synchronization. However, if the threads are working on different instances of the class, they can execute the synchronized methods simultaneously without interference.

??? example "Instance-Level Locking Example"
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

    !!! info "Why does this work ?"
        Since both threads are operating on the same `Counter` object, only one thread at a time can execute the `increment()` method due to instance-level locking.


### **Class-Level Locking**

A static synchronized method locks on the Class object (i.e., `ClassName.class`) rather than on an instance. This ensures that all threads calling static methods on the class are synchronized.

```java
public synchronized static void staticIncrement() {
    // Lock acquired on the class object (Counter.class)
}
```

If a thread calls a static synchronized method, no other thread can access any other static synchronized method of that class until the lock is released. However, threads can still call non-static synchronized methods because instance-level locks are separate from class-level locks. Additionally, threads working on different instances of the class can execute non-static synchronized methods simultaneously, as each instance has its own lock, but static synchronized methods remain locked across all instances.

??? example "Class-Level Locking Example"
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



## **Synchronized Blocks**

A synchronized block provides more control than a synchronized method. You can choose which object’s intrinsic lock to use, instead of locking the entire method.


!!! tip "When to use ?"
    - To improve performance instead of synchronizing the entire method, only the critical section needs to be synchronized.
    ```java
    public void increment() {
        synchronized (this) {  // Locking on the current instance
            count++;
        }
    }
    ```
    
    - To lock on specific objects you can use any object as the lock, not just the instance or class.
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

??? example "Synchronized Block with Fine-Grained Control Example"
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


## **Best Practices**

- Synchronizing unnecessary code slows down the application so use when necessary.

- Minimize the scope of synchronization so use synchronized blocks rather than whole methods to reduce contention.

- Ensure you synchronize on the same object across threads to avoid incorrect locking.

- Nested synchronized blocks can lead to deadlock. Use consistent lock ordering.


## **Potential Issues**

- Deadlock, occurs if two or more threads are waiting for each other to release locks.

- Performance Bottlenecks, overusing synchronization can lead to contention, where threads are constantly waiting to acquire locks.

- Livelock, threads keep responding to each other without making progress (e.g., both threads keep yielding the lock to each other).

## **Summary**

Locking is essential in multithreaded programming to ensure thread safety and prevent race conditions. Java’s `synchronized` provides intrinsic locking with mutual exclusion and visibility guarantees. It supports instance-level locking (`this`) and class-level locking (`ClassName.class`). The JVM optimizes synchronization using biased, lightweight, and heavyweight locks to enhance performance. Synchronized blocks offer finer control by locking specific critical sections. Best practices include minimizing synchronization scope, using consistent locks, and avoiding nested locks to prevent deadlocks. Key issues to watch for are deadlocks, performance bottlenecks, and livelocks.

---