# **Issues with Locking - Starvation**

Locking mechanisms in Java, while essential for ensuring thread safety in multithreaded applications, can introduce various issues if not used properly. 

In this article, we’ll explore how starvation occur, how to prevent them, and practical examples of various techniques to detect and resolve starvation.

## **What is Starvation ?**

Starvation is a condition where low-priority threads are unable to gain access to resources because higher-priority threads or unfair scheduling policies monopolize CPU time or locks. As a result, the low-priority thread starves and never gets the chance to run, even though it is ready to execute.

This issue can manifest in multithreaded programs when locks or resources are continuously granted to specific threads, leaving others waiting indefinitely. It can occur not only due to CPU scheduling but also due to improper locking strategies, unfair algorithms, or resource starvation.

---

## **Causes of Starvation**

- Unfair Locking Mechanism, When using unfair locks, the lock may always favor threads that request it recently over those that have been waiting longer. 

- Priority Inversion, High-priority threads monopolize the CPU and low-priority threads rarely get scheduled. This is especially problematic in priority-based scheduling systems.

- Thread Contention and Resource Starvation, Too many threads competing for a limited number of resources (like I/O or locks), some threads may always be able to acquire the resources, leaving others waiting indefinitely.

- Busy-Waiting Loops, Threads that continuously request resources or repeatedly try to acquire locks without releasing the CPU can cause other threads to starve.

- Thread Prioritization in Java, If threads have different priorities, the JVM might schedule higher-priority threads more frequently, leading to starvation of lower-priority threads.

---

## **Example of Starvation**

???+ example "Starvation with Unfair Lock Example"
    ```java
    import java.util.concurrent.locks.ReentrantLock;

    public class StarvationDemo {
        private static final ReentrantLock lock = new ReentrantLock();  // Unfair lock

        public static void main(String[] args) {
            Runnable task = () -> {
                while (true) {
                    if (lock.tryLock()) {
                        try {
                            System.out.println(Thread.currentThread().getName() + " got the lock!");
                            break;
                        } finally {
                            lock.unlock();
                        }
                    } else {
                        System.out.println(Thread.currentThread().getName() + " waiting...");
                    }
                }
            };

            Thread highPriority = new Thread(task, "High-Priority");
            highPriority.setPriority(Thread.MAX_PRIORITY);

            Thread lowPriority = new Thread(task, "Low-Priority");
            lowPriority.setPriority(Thread.MIN_PRIORITY);

            highPriority.start();
            lowPriority.start();
        }
    }
    ```

    !!! info "Explanation"
        - The ReentrantLock is unfair by default.
        - The high-priority thread has a better chance of acquiring the lock repeatedly.
        - The low-priority thread may never acquire the lock, resulting in starvation.

---

## **How to Avoid ?**

### **Use Fair Locks**

Using fair locks ensures that the longest-waiting thread gets the lock first. This prevents threads from skipping the queue and ensures all threads get a chance to execute.

???+ example "Fair Lock Example"
    ```java
    import java.util.concurrent.locks.ReentrantLock;

    public class FairLockDemo {
        private static final ReentrantLock lock = new ReentrantLock(true);  // Fair lock

        public static void main(String[] args) {
            Runnable task = () -> {
                while (true) {
                    if (lock.tryLock()) {
                        try {
                            System.out.println(Thread.currentThread().getName() + " got the lock!");
                            break;
                        } finally {
                            lock.unlock();
                        }
                    } else {
                        System.out.println(Thread.currentThread().getName() + " waiting...");
                    }
                }
            };

            Thread highPriority = new Thread(task, "High-Priority");
            Thread lowPriority = new Thread(task, "Low-Priority");

            highPriority.setPriority(Thread.MAX_PRIORITY);
            lowPriority.setPriority(Thread.MIN_PRIORITY);

            highPriority.start();
            lowPriority.start();
        }
    }
    ```

!!! note
    - With the fair lock enabled, the thread that waits the longest gets access to the lock first.
    - This prevents starvation and ensures fairness among threads.


### **Avoid Priority-Based Scheduling**

Although Java allows you to assign priorities to threads, the JVM’s thread scheduler may not always honor them consistently. It’s generally recommended to avoid relying on thread priorities for critical tasks, If you need to control thread scheduling, use fair locks or condition variables instead of thread priorities.

### **Backoff Strategies**

Using backoff strategies (delays) between retries can help reduce contention for resources. This ensures that no thread monopolizes the CPU by continuously attempting to acquire a resource.

???+ example "Backoff Strategy Example"
    ```java
    import java.util.concurrent.locks.ReentrantLock;

    public class BackoffDemo {
        private static final ReentrantLock lock = new ReentrantLock();

        public static void main(String[] args) {
            Runnable task = () -> {
                while (true) {
                    if (lock.tryLock()) {
                        try {
                            System.out.println(Thread.currentThread().getName() + " got the lock!");
                            break;
                        } finally {
                            lock.unlock();
                        }
                    } else {
                        System.out.println(Thread.currentThread().getName() + " waiting...");
                        try {
                            Thread.sleep((long) (Math.random() * 100));  // Random delay
                        } catch (InterruptedException e) {
                            Thread.currentThread().interrupt();
                        }
                    }
                }
            };

            Thread t1 = new Thread(task, "Thread-1");
            Thread t2 = new Thread(task, "Thread-2");

            t1.start();
            t2.start();
        }
    }
    ```

!!! note
    Random delays ensure that threads do not engage in busy-waiting loops, reducing contention and improving fairness.


### **Use Thread Pools**

When dealing with many concurrent tasks, using a thread pool ensures that threads are fairly scheduled and resources are shared efficiently. 

???+ example "Using ThreadPoolExecutor Example"
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class ThreadPoolDemo {
        public static void main(String[] args) {
            ExecutorService executor = Executors.newFixedThreadPool(2);

            Runnable task = () -> {
                System.out.println(Thread.currentThread().getName() + " is running");
            };

            for (int i = 0; i < 5; i++) {
                executor.submit(task);
            }

            executor.shutdown();
        }
    }
    ```

!!! note
    Using thread pools avoids creating too many threads and ensures fair resource sharing.


### **Avoid Long Critical Sections**

- If a thread holds a lock for too long, it can cause starvation for other threads.
- Split large critical sections into smaller synchronized blocks to reduce lock contention.


### **Use Condition Variables**

Instead of relying on priorities or busy-waiting, use `Condition` objects with ReentrantLock to manage thread coordination efficiently.

???+ example "Condition Variables Example"
    ```java
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.Lock;
    import java.util.concurrent.locks.ReentrantLock;

    public class ConditionDemo {
        private static final Lock lock = new ReentrantLock();
        private static final Condition condition = lock.newCondition();

        public static void main(String[] args) {
            new Thread(() -> {
                lock.lock();
                try {
                    System.out.println("Waiting...");
                    condition.await();  // Wait for a signal
                    System.out.println("Resumed");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    lock.unlock();
                }
            }).start();

            new Thread(() -> {
                lock.lock();
                try {
                    Thread.sleep(1000);
                    condition.signal();  // Signal the waiting thread
                    System.out.println("Signaled");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                } finally {
                    lock.unlock();
                }
            }).start();
        }
    }
    ```
!!! note
    Using conditions helps avoid busy-waiting and ensures efficient thread signaling.

---

## **Best Practices**

- Use Fair Locks to prevent thread starvation.
- Avoid priority-based thread scheduling for critical operations.
- Implement backoff strategies to reduce contention.
- Use thread pools to manage threads efficiently.
- Minimize the time spent holding locks by breaking up large critical sections.
- Use Condition variables for better control of thread synchronization.

---

## **Summary**

Starvation is a subtle but serious issue in multithreaded programs, particularly when some threads are prioritized over others or when resources are monopolized by specific threads. By using fair locks, thread pools, backoff strategies, and avoiding long critical sections.

---