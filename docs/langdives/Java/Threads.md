# **Threads**

Java offers multithreading to perform multiple tasks concurrently, improving performance and responsiveness. This deep dive covers every key concept of Java threading with detailed explanations and code examples. 

Before that let's have a quick rewind of fundamental concept of concurrency and parallelism.

---

## **Concurrency and Parallelism**
**Concurrency**: Multiple tasks start, run, and complete in overlapping time periods (not necessarily simultaneously).

**Parallelism**: Multiple tasks run exactly at the same time (requires multi-core processors).

We have another article where we gone through fundamentals of concurrency and parallelism in depth though we cover some of the stuff here to but its recommeneded to go through this artice [Concurrency and Parallelism](../../fundamentaldives/FundamentalConcepts/ConcurrencyParallelism.md)

Java achieves both using threads, thread pools, and various libraries such as Executors, Fork/Join Framework, and Streams API, We will go through them one by one and in this article we mostly cover Threads.

---


## **What is a Thread?**

A thread is a lightweight sub-process. A Java program has at least one thread — the **main thread**, which starts with the `main()` method. You can create additional threads to execute code concurrently. Each thread shares the same process memory, but has its own **stack, registers**, and **program counter**.

---

## **How to Create ?**

You can create a thread in two ways:

```java title="Extending the Thread class"
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();  // Start the thread
    }
}
```

```java title="Implementing the Runnable interface"
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable running: " + Thread.currentThread().getName());
    }
}

public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyRunnable());
        t1.start();  // Start the thread
    }
}
```
!!! note "When to Use ?"
    - **`Runnable`**: When you need to inherit from another class, use `Runnable` since Java does not support multiple inheritance.
    - **`Thread`**: If your class does not need to extend any other class, extending `Thread` might be more intuitive.

---

## **Thread Lifecycle**

A thread in Java goes through the following states:

!!! abstract "Thread Lifecycle"
    - **NEW**: Thread is created but not started yet.
    - **RUNNABLE**: Thread is ready to run or running but waiting for CPU time.
    - **BLOCKED / WAITING / TIMED_WAITING**: Thread is waiting for a resource or condition.
    - **RUNNING**: Thread is executing.
    - **TERMINATED**: Thread has completed execution or stopped due to an exception.

???+ example "Example: Thread Lifecycle"
    ```java
    class MyThread extends Thread {
        public void run() {
            System.out.println("Running thread: " + Thread.currentThread().getName());
        }
    }

    public class Main {
        public static void main(String[] args) throws InterruptedException {
            MyThread t1 = new MyThread();  // NEW
            t1.start();  // RUNNABLE

            // Join to wait for the thread to complete
            t1.join();  // Terminated once finished
            System.out.println("Thread has terminated.");
        }
    }
    ```

!!! note
    - **Blocked State**: Occurs when the thread is waiting for a monitor lock (e.g., waiting for I/O or a lock in synchronized methods).
    - **Waiting State**: Occurs when a thread calls `wait()` or waits indefinitely.
    - **Timed Waiting**: Happens when `sleep()` or `join(time)` is invoked, meaning the thread will resume after a specific time.

---

## **Daemon Threads**

A daemon thread is a background thread that provides support services, like the garbage collector. It does not prevent the JVM from shutting down once all user threads are completed.

???+ example "Example of Daemon Thread"
    ```java
    class DaemonThread extends Thread {
        public void run() {
            if (Thread.currentThread().isDaemon()) {
                System.out.println("This is a daemon thread.");
            } else {
                System.out.println("This is a user thread.");
            }
        }
    }

    public class Main {
        public static void main(String[] args) {
            DaemonThread t1 = new DaemonThread();
            t1.setDaemon(true);  // Set as daemon thread
            t1.start();

            DaemonThread t2 = new DaemonThread();
            t2.start();
        }
    }
    ```

!!! note "When to use Daemon Threads ?"
    For background tasks like logging, garbage collection, or monitoring services.

---

## **Thread Priority**

Java assigns a priority to each thread, ranging from 1 (MIN_PRIORITY) to 10 (MAX_PRIORITY). The default priority is 5 (NORM_PRIORITY). Thread priority affects scheduling, but it’s platform-dependent — meaning it doesn’t guarantee execution order.

???+ example "Setting Thread Priority Example"
    ```java
    class PriorityThread extends Thread {
        public void run() {
            System.out.println("Running thread: " + Thread.currentThread().getName() +
                            " with priority: " + Thread.currentThread().getPriority());
        }
    }

    public class Main {
        public static void main(String[] args) {
            PriorityThread t1 = new PriorityThread();
            PriorityThread t2 = new PriorityThread();

            t1.setPriority(Thread.MIN_PRIORITY);  // Priority 1
            t2.setPriority(Thread.MAX_PRIORITY);  // Priority 10

            t1.start();
            t2.start();
        }
    }
    ```

!!! note "When to use Priority Threads"
    Only when certain tasks should have **preferential scheduling** over others. However, Java thread scheduling is **not guaranteed**, so don't rely solely on priority.

---

## **Thread Synchronization**

When multiple threads access shared resources (like variables), synchronization ensures that only one thread modifies the resource at a time. Use the `synchronized` keyword to prevent race conditions.

???+ example "Synchronization Example"
    ```java
    class Counter {
        private int count = 0;

        public synchronized void increment() {
            count++;  // Only one thread can increment at a time
        }

        public int getCount() {
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

            System.out.println("Final count: " + counter.getCount());
        }
    }
    ```
!!! note "When to use Synchronization ?"
    When multiple threads access **critical sections** of code to avoid inconsistent data.

---

## **Inter-thread Communication**

Java allows threads to communicate using **wait-notify** methods, avoiding **busy waiting**. 

- **`wait()`**: Makes the thread wait until another thread notifies it.
- **`notify()`**: Wakes up a single waiting thread.
- **`notifyAll()`**: Wakes up all waiting threads.

???+ example "Inter-thread Communication Example"
    ```java
    class SharedResource {
        private int value;
        private boolean available = false;

        public synchronized void produce(int val) throws InterruptedException {
            while (available) {
                wait();  // Wait if value is already available
            }
            value = val;
            available = true;
            System.out.println("Produced: " + value);
            notify();  // Notify the consumer thread
        }

        public synchronized void consume() throws InterruptedException {
            while (!available) {
                wait();  // Wait if value is not available
            }
            System.out.println("Consumed: " + value);
            available = false;
            notify();  // Notify the producer thread
        }
    }

    public class Main {
        public static void main(String[] args) {
            SharedResource resource = new SharedResource();

            Thread producer = new Thread(() -> {
                try {
                    for (int i = 1; i <= 5; i++) resource.produce(i);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            Thread consumer = new Thread(() -> {
                try {
                    for (int i = 1; i <= 5; i++) resource.consume();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            producer.start();
            consumer.start();
        }
    }
    ```

---

## **Thread-Local Variables**

`ThreadLocal` provides a way to create **thread-isolated variables**. Each thread gets its own copy of the variable, and changes made by one thread do not affect others. This is useful when you don’t want threads to share a common state.

???+ example "ThreadLocal Usage Example"
    ```java
    public class ThreadLocalExample {
        private static ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 1);

        public static void main(String[] args) {
            Thread t1 = new Thread(() -> {
                threadLocal.set(100);
                System.out.println("Thread 1: " + threadLocal.get());
            });

            Thread t2 = new Thread(() -> {
                threadLocal.set(200);
                System.out.println("Thread 2: " + threadLocal.get());
            });

            t1.start();
            t2.start();
        }
    }
    ```

!!! note "When to use ?"
    Useful in multi-threaded environments (like database transactions) where each thread needs its own context without interference from other threads.

---

## **Volatile Variables**

The **`volatile`** keyword ensures **visibility of changes** to variables across threads. Without `volatile`, thread-local caches may not reflect the latest changes made by other threads.

???+ example "Volatile Example"
    ```java
    public class VolatileExample {
        private static volatile boolean running = true;

        public static void main(String[] args) {
            Thread t = new Thread(() -> {
                while (running) {
                    // Busy-wait
                }
                System.out.println("Thread stopped.");
            });

            t.start();

            try { Thread.sleep(1000); } catch (InterruptedException e) { }
            running = false;  // Change will be visible to other threads
        }
    }
    ```

!!! note "When to Use Volatile"
    Use volatile for variables accessed by multiple threads without needing synchronization (e.g., **flags**).


### **When to Use `volatile` ?**

- When Multiple threads are reading and writing a shared variable.
- When No complex operations (like increments) are performed on the variable.
- When you don’t want to use **synchronized** due to overhead, but **memory visibility** is still required.

???+ example "Example Where `volatile` is Necessary"
    ```java
    class VolatileExample {
        private volatile boolean running = true;

        public void stop() {
            running = false;  // Change becomes immediately visible to other threads
        }

        public void run() {
            while (running) {
                // Do something
            }
            System.out.println("Thread stopped.");
        }

        public static void main(String[] args) throws InterruptedException {
            VolatileExample example = new VolatileExample();

            Thread t = new Thread(example::run);
            t.start();

            Thread.sleep(1000);
            example.stop();  // Stop the thread
        }
    }
    ```

    ??? info "Explanation"
        Here, `volatile` ensures that the **change to `running`** made by the `stop()` method is **immediately visible** to the thread executing `run()`. Without `volatile`, the `run()` thread might **never see the change** and keep running indefinitely.

### **When Not to Use `volatile` ?**

- When Operations depend on the current value (like `count++`).
- When You need atomic operations (which require locks or `Atomic` classes).

???+ example "Problem with Volatile for Non-Atomic Operations"
    ```java
    class Counter {
        private volatile int count = 0;

        public void increment() {
            count++;  // Not atomic! Two threads can still read the same value.
        }

        public int getCount() {
            return count;
        }
    }
    ```

    !!! warning "Issue"
        Even though `count` is marked `volatile`, `count++` is **not atomic**. Two threads could read the same value and increment it, leading to **lost updates**. To fix this, use **synchronized** or `AtomicInteger`.

## **Volatile vs Synchronized**

### **No `synchronized` or `volatile` ?**

If you don’t use `volatile` or `synchronized`, some dangerous scenarios can occur. Like this:

???+ example Without Synchronization and Volatile
    ```java
    class SharedResource {
        private boolean available = false;

        public void produce() {
            available = true;  // Change not guaranteed to be visible immediately
        }

        public void consume() {
            while (!available) {
                // Busy-waiting, might never see the change to `available`
            }
            System.out.println("Consumed!");
        }
    }
    ```

    !!! danger "Problem"
        If `available` is not marked **`volatile`**, the change made by `produce()` might **not be visible** to the `consume()` thread immediately. The consumer thread might be stuck in an infinite loop because it doesn't see the latest value of `available`.

!!! note
    - Threads may **cache variables** locally in **CPU registers or thread-local memory**.
    - **No guarantee** that updates made by one thread will be immediately **visible to other threads** unless `volatile` or `synchronized` is used.

### **`Synchronized` over `volatile` ?**

Let's go through an example where its okay to use just synchroinized instead of volatile

???+ example
    ```java
    public synchronized void produce(int val) throws InterruptedException {
        while (available) {
            wait();  // Wait if value is already available
        }
        value = val;
        available = true;
        System.out.println("Produced: " + value);
        notify();  // Notify the consumer thread
    }
    ```

    **Synchronized Keyword**: 

    - When a thread enters a **`synchronized` block** or method (like `produce()` or `consume()`), it **acquires a lock** on the `SharedResource` object. This ensures **mutual exclusion** — only one thread can access the critical section at a time.
    - **Memory visibility**: Synchronization **flushes the thread-local cache** to **main memory** when a thread exits the `synchronized` block. This ensures that the **latest value of `available`** is visible to all other threads.

    **Wait-Notify Mechanism**:

    - The **`wait()` and `notify()`** methods release and acquire the lock, enforcing **happens-before relationships** (meaning operations before `notify()` or `wait()` are visible to other threads).
    - This ensures that if one thread sets `available = true` in `produce()`, another thread waiting in `consume()` will **see the updated value** when it wakes up.

    Because this code uses `synchronized` methods and wait-notify, the necessary memory visibility is achieved without needing `volatile`. 


### **Differences**

| **Aspect**              | **`volatile`**                          | **`synchronized`**                     |
|-------------------------|------------------------------------------|----------------------------------------|
| **Visibility**          | Ensures visibility of changes.           | Ensures visibility and atomicity.     |
| **Atomicity**           | Not guaranteed.                         | Guaranteed (only one thread at a time). |
| **Performance**         | Faster (no locking).                    | Slower (locking involved).            |
| **Use Case**            | For flags, simple state updates.         | For complex operations, critical sections. |
| **Overhead**            | Low (no blocking).                      | High (involves blocking and context switches). |

---

## **Thread Memory**

The memory consumption per thread and the maximum number of threads in Java depend on several factors, such as:

- JVM and OS configurations (32-bit vs 64-bit JVM).
- Thread stack size (which can be configured).
- Available system memory.
- OS-imposed limits.


### **Memory used by Thread**

Each Java thread consumes two key areas of memory:

- Java Thread Stack Memory (allocated per thread).
- Native Thread Metadata / Overhead (handled by the OS).

**Thread Stack Memory:** Each thread gets its own stack, which holds Local variables (primitives, references), Method call frames, Intermediate results during method execution.

!!! note 
    The default stack size depends on the JVM and platform:

    - **Linux / macOS / Windows 64-bit**: ~1 MB per thread.
    - **32-bit JVM**: ~320 KB to 512 KB per thread.

You can change the stack size with the **`-Xss` JVM option**:
```bash
java -Xss512k YourProgram
```

**Native Thread Metadata:** In addition to stack memory, the **OS kernel** allocates **metadata** per thread (for thread control structures). This varies by platform but is typically in the range of **8 KB to 16 KB** per thread.


### **Memory per Thread ?**

The typical memory consumption per thread:

- **Stack size**: 512 KB to 1 MB.
- **Native metadata overhead**: ~16 KB (depends on OS).

Thus, a **single thread** could use **~1 MB to 1.1 MB** of memory.


### **Max Threads you Can Create ?**

The number of threads you can create depends on:

- Available system memory.
- JVM heap size (configurable with `-Xmx`).
- Thread stack size (configurable with `-Xss`).
- OS limits on threads per process.

???+ example "Practical Calculation Example"
    Let's say:

    - Available physical memory: 8 GB.
    - JVM heap size: 2 GB (`-Xmx2g`).
    - Available memory for threads: 6 GB (8 GB - 2 GB heap).
    - Stack size per thread: 1 MB (`-Xss1m`).

    **Maximum threads** = `6 GB / 1 MB per thread = ~6000 threads`.

!!! note "OS Limits on Threads"
    Even if memory allows for thousands of threads, the OS imposes limits:

    - Linux: 1024 - 32,768 threads (configurable with `/etc/security/limits.conf`).
    - Windows: Around 2000-3000 threads per process.

### **Too Many Threads Created ?**

- `OutOfMemoryError: unable to create new native thread` Occurs when the OS can't allocate more native threads.

- Performance Degradation as context switching between many threads becomes expensive, leading to CPU thrashing.

### **Optimizing Thread Usage**

Rather than creating many threads manually, **use thread pools** to manage a fixed number of threads efficiently:

???+ example "Thread Pools Example"
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class ThreadPoolExample {
        public static void main(String[] args) {
            ExecutorService executor = Executors.newFixedThreadPool(10);
            for (int i = 0; i < 100; i++) {
                executor.submit(() -> {
                    System.out.println("Running thread: " + Thread.currentThread().getName());
                });
            }
            executor.shutdown();
        }
    }
    ```
**Thread pools** reuse threads, reducing memory usage and improving performance.

### **How to Increase Max Thread ?**

On Linux, you can increase the maximum threads per process

```bash title="Check current limits"
ulimit -u  # Max user processes
```

```bash title="Increase limit (temporary)"
ulimit -u 65535
```

``` title="Permanent change: Edit '/etc/security/limits.conf' and add"
your_user_name  hard  nproc  65535
your_user_name  soft  nproc  65535
```

!!! note "Key points"

    - **Memory per thread**: Typically **1 MB to 1.1 MB**.
    - **Number of threads**: Limited by **available memory** and **OS restrictions**.
    - **Practical upper limit**: A system with **8 GB RAM** and **1 MB stack size** can support around **6000 threads**, but OS limits (like **Linux: ~32K**, **Windows: ~2K**) may restrict this.

---