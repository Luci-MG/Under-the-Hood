# **Issues with Locking - Other Issues**

Locking mechanisms in Java, while essential for ensuring thread safety in multithreaded applications, can introduce various issues if not used properly. 

We will cover key locking issues in Java in this article like race conditions, thread contention, missed signals, nested locks, overuse of locks, and performance impact. Each section contains causes, examples, solutions, and best practices to avoid or mitigate these issues.

---

## **Race Conditions Despite Locking**

??? danger "Cause"
    A race condition occurs when multiple threads access a shared resource without proper synchronization, leading to inconsistent results based on the timing of thread execution. Even with partial locks, a shared variable may still be accessed inconsistently if not protected properly.

    !!! example "Race Condition Example"
        ```java
        class Counter {
            private int count = 0;

            public void increment() {
                synchronized (this) {
                    count++;
                }
            }

            public int getCount() {
                // Not synchronized, potential race condition.
                return count;
            }
        }
        ```

        !!! bug "Problem"
            - Thread 1 increments `count` to 1.
            - Before `count` can be read by Thread 2, Thread 3 increments it again.
            - As `getCount()` is not synchronized, the returned value may skip increments due to improper timing.

??? success "Solution and Best Practices"
    - Always synchronize access to shared variables, even on read operations if other threads can modify the data.
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
    ```


    - Use `AtomicInteger` if possible for thread-safe increments:
    ```java
    import java.util.concurrent.atomic.AtomicInteger;

    class Counter {
        private final AtomicInteger count = new AtomicInteger(0);

        public void increment() {
            count.incrementAndGet();
        }

        public int getCount() {
            return count.get();
        }
    }
    ```

---

## **Contention & Performance Issues**

??? danger "Cause"
    When multiple threads compete for the same lock, they spend time waiting for the lock to become available, reducing throughput and performance.

    !!! example "Contention Example"
        ```java
        class BankAccount {
            private int balance = 100;

            public synchronized void withdraw(int amount) {
                balance -= amount;
            }

            public synchronized int getBalance() {
                return balance;
            }
        }
        ```
        
        !!! bug "Problem"
            If multiple threads frequently access the `withdraw()` method, contention for the lock will occur, degrading performance.

??? success "Solution and Best Practices"
    - Minimize the Scope of Synchronized Blocks:
    ```java
    public void withdraw(int amount) {
        if (amount <= 0) return;

        synchronized (this) {
            balance -= amount;
        }
    }
    ```

    - Use `ReadWriteLock` if reads dominate writes:
    ```java
    import java.util.concurrent.locks.ReentrantReadWriteLock;

    class BankAccount {
        private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
        private int balance = 100;

        public void withdraw(int amount) {
            lock.writeLock().lock();
            try {
                balance -= amount;
            } finally {
                lock.writeLock().unlock();
            }
        }

        public int getBalance() {
            lock.readLock().lock();
            try {
                return balance;
            } finally {
                lock.readLock().unlock();
            }
        }
    }
    ```

    - Use lock-free data structures like `AtomicInteger` or `ConcurrentHashMap` to reduce contention.

---

## **Missed Signals & Lost Wake-ups**

??? danger "Cause"
    When a thread misses a `notify()` signal because it was not yet waiting on the lock, a lost wake-up occurs. This results in threads waiting indefinitely for a signal that has already been sent.

    !!! example "Lost Wake-Up Example"
        ```java
        public synchronized void produce() throws InterruptedException {
            while (available) {
                wait();  // Missed if notify() was called before reaching here.
            }
            available = true;
            notify();
        }
        ```

??? success "Solution and Best Practices"
    - Always Use a `while` Loop Instead of `if`, The `while` loop ensures the thread rechecks the condition after being notified (to avoid spurious wake-ups).
    ```java
    public synchronized void produce() throws InterruptedException {
        while (available) {
            wait();
        }
        available = true;
        notify();
    }
    ```
    

    - Use `Condition` Variables with `ReentrantLock` for finer control:
    ```java
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean available = false;

    public void produce() throws InterruptedException {
        lock.lock();
        try {
            while (available) {
                condition.await();  // Wait on condition.
            }
            available = true;
            condition.signal();
        } finally {
            lock.unlock();
        }
    }
    ```

---

## **Nested Locks & Ordering Issues**

??? danger "Cause"
    Using multiple locks can cause deadlocks if threads acquire locks in different orders.

    !!! example "Deadlock Example"
        ```java
        synchronized (lock1) {
            synchronized (lock2) {
                // Critical section
            }
        }
        ```
        !!! bug "Problem"
            If Thread 1 acquires `lock1` and Thread 2 acquires `lock2`, both threads will wait indefinitely for each other’s lock, resulting in a deadlock.

??? success "Solution and Best Practices"
    - Use a consistent lock acquisition order across all threads.
    - Use `tryLock()` with timeout to avoid blocking indefinitely:
    ```java
    if (lock1.tryLock(1, TimeUnit.SECONDS)) {
        if (lock2.tryLock(1, TimeUnit.SECONDS)) {
            try {
                // Critical section
            } finally {
                lock2.unlock();
            }
        }
        lock1.unlock();
    }
    ```

---

## **Overuse of Locks**

??? danger "Cause"
    Using too many locks or locking too frequently can reduce parallelism, resulting in poor scalability, If every method in a class is synchronized, threads will frequently block each other, reducing concurrency and efficiency.

??? success "Solution and Best Practices"
    - Minimize Lock Usage by synchronizing only critical sections.
    - Use concurrent collections (like `ConcurrentHashMap`) instead of traditional synchronized collections:
    ```java
    ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
    ```

---

## **Overhead of Locking**

??? danger "Cause"
    Locking adds overhead in the form of:
    - Context switches between threads.
    - CPU cache invalidation.
    - JVM's monitor management for intrinsic locks.

    !!! bug "Performance Issues with Synchronized Code"
        Excessive locking causes contention and frequent context switches, impacting throughput and latency.

??? success "Solution and Best Practices"
    - Use atomic classes like `AtomicInteger` for simple counters.
    - Use lock-free algorithms whenever possible.
    - Use thread pools to reduce the overhead of creating and managing threads.

---

## **Best Practices**

- Use `tryLock()` for non-blocking operations:
   ```java
   if (lock.tryLock()) {
       try {
           // Critical section
       } finally {
           lock.unlock();
       }
   }
   ```

- Minimize the scope of synchronized blocks to reduce contention.

- Use fair locks to avoid starvation:
   ```java
   ReentrantLock lock = new ReentrantLock(true);  // Fair lock
   ```

- Use lock-free data structures when possible (e.g., `ConcurrentHashMap`).

- Monitor and detect deadlocks using tools like VisualVM or JConsole.

- Follow consistent lock ordering to prevent deadlocks.

---

## **Summary**

Locking is essential to ensure thread safety, but improper use can lead to issues such as race conditions, deadlocks, livelocks, contention, and performance degradation. Understanding these issues and following best practices will help you write efficient, scalable, and thread-safe code. Using fine-grained locks and concurrent utilities wisely to maximize concurrency while minimizing risks.

---