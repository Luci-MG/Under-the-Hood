# Locking.

Locking is an essential concept in multithreaded programming to prevent race conditions and ensure thread safety. When multiple threads access shared resources, locks ensure that only one thread accesses the critical section at a time.

This article covers read-write locks.

---

## **What is Locking ?**

**Locking** is a way to ensure that **only one thread at a time** executes a critical section or modifies a shared resource, Without proper locks, multiple threads may interfere with each other, leading to data inconsistency or unexpected behavior (race conditions).

Java offers various **locking mechanisms**, from **synchronized blocks** to **explicit locks** like `ReentrantLock` and `ReentrantReadWriteLock`.

---

## **What is `ReentrantReadWriteLock`?**

The `ReentrantReadWriteLock` is a specialized lock from Java’s `java.util.concurrent.locks` package, designed to improve performance in concurrent systems by separating read and write operations. It provides more efficient locking behavior when the majority of operations are read-only, allowing multiple readers to access the shared resource simultaneously but blocking writers until all reading operations are complete.

A `ReentrantReadWriteLock` maintains two types of locks:

- **Read Lock**: Multiple threads can acquire the **read lock** simultaneously if no thread holds the **write lock**.
- **Write Lock**: Allows only **one thread** to acquire the lock. All readers and other writers are blocked until the write operation completes.

This separation helps optimize performance for **read-heavy workloads**.


???+ example
    ```java
    import java.util.concurrent.locks.ReentrantReadWriteLock;

    class SharedResource {
        private int data = 0;
        private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

        public void write(int value) {
            lock.writeLock().lock();  // Acquire write lock
            try {
                data = value;
                System.out.println("Data written: " + value);
            } finally {
                lock.writeLock().unlock();  // Release write lock
            }
        }

        public int read() {
            lock.readLock().lock();  // Acquire read lock
            try {
                System.out.println("Data read: " + data);
                return data;
            } finally {
                lock.readLock().unlock();  // Release read lock
            }
        }
    }

    public class Main {
        public static void main(String[] args) throws InterruptedException {
            SharedResource resource = new SharedResource();

            // Writer thread
            Thread writer = new Thread(() -> resource.write(42));

            // Reader threads
            Thread reader1 = new Thread(() -> resource.read());
            Thread reader2 = new Thread(() -> resource.read());

            writer.start();
            reader1.start();
            reader2.start();

            writer.join();
            reader1.join();
            reader2.join();
        }
    }
    ```

---

## **How it Works ?**

- Multiple Threads can acquire the read lock simultaneously if no thread holds the write lock.
- A single thread can acquire the write lock, blocking other readers and writers.
- Write locks are exclusive, meaning only one thread can acquire it at a time.
- Read locks are shared, allowing multiple readers to read concurrently.

---

## **Key Features**

- **Fair vs Unfair Locking**: By default, `ReentrantReadWriteLock` uses **unfair locking** for better performance, but they may introduce performance overhead
     ```java
     ReentrantReadWriteLock lock = new ReentrantReadWriteLock(true);  // Fair lock
     ```

- **Reentrancy**: A thread holding a **write lock** can **acquire the read lock** without blocking, This is useful for scenarios where a thread needs to read and modify shared data atomically.

- **Downgrading**: A thread can **downgrade** a write lock to a read lock without releasing the lock entirely.
     ```java
     lock.writeLock().lock();
     try {
         // Critical write section
         lock.readLock().lock();  // Downgrade to read lock
     } finally {
         lock.writeLock().unlock();  // Release write lock
     }
     ```

---

## **Common Problems**

???+ danger "Write Starvation"
     In scenarios with frequent readers, a writer may starve because readers keep acquiring the read lock, delaying the writer indefinitely.

    !!! success "Solution"
        Use a **fair lock**

        ```java
        ReentrantReadWriteLock lock = new ReentrantReadWriteLock(true);  // Enable fairness
        ```
    
        Fair locks ensure that **waiting writers get a chance to execute** after the current readers finish.


???+ danger "DeadLock"
    If a read lock and write lock are acquired in an inconsistent order across multiple threads, it can lead to deadlock.

    !!! example "Deadlock example"
        ```java
        Thread 1: Acquire write lock -> Attempt to acquire read lock (blocks)
        Thread 2: Acquire read lock -> Attempt to acquire write lock (blocks)
        ``` 

    !!! success "Solution"
        - Ensure consistent lock ordering across all threads.
        - Avoid nested locks when using both read and write locks.


???+ danger "Performance Degradation with Too Many Write Operations"

    If there are frequent write operations, the system behaves similarly to using a normal ReentrantLock, as readers must wait for writers to release the lock.

    !!! success "Solution"
        Use lock-free data structures (like `AtomicReference`) or ReadWriteLock only when reads significantly outnumber writes.


???+ danger "Incorrect Use of Lock Downgrading"

    If a thread holding the write lock tries to release it before acquiring the read lock, **data inconsistencies** can occur.

    !!! success "Correct Lock Downgrading Example"
        ```java
        lock.writeLock().lock();
        try {
            // Write critical section
            lock.readLock().lock();  // Downgrade to read lock
        } finally {
            lock.writeLock().unlock();  // Release write lock
        }
        // Perform read operations under the read lock.
        ```

---

## **When to Use ?**

- Read-heavy Workloads, when the majority of operations are reads and only a few writes occur.

- Improved Performance over `ReentrantLock`, In scenarios where multiple threads need to read data simultaneously, `ReentrantReadWriteLock` improves performance by allowing concurrent reads.

- When You Need to Separate Read and Write Operations, use it to prevent blocking readers unnecessarily during write operations.

---

## **When Not to Use ?**

- Write-heavy Applications, If writes occur frequently, the write lock will block readers, negating the benefits of the read-write separation.

- Simple Synchronization Requirements, If your application doesn’t require complex locking behavior, use `synchronized` or `ReentrantLock` instead.

- Lock-free Alternatives Available, consider atomic classes or concurrent data structures (e.g., `ConcurrentHashMap`) if they meet your needs.

---

## **Best Practices**

- Use Fair Locks Only When Necessary, Use **unfair locks** by default for better performance. Enable **fairness** only if starvation becomes an issue.

- Minimize the Duration of Locks, Avoid holding read or write locks longer than necessary to **reduce contention**.

- Use Lock Downgrading Cautiously, Always acquire the read lock before releasing the write lock to avoid inconsistencies.

- Monitor Lock Usage, Use tools like **VisualVM** or **JConsole** to monitor thread activity and detect potential contention or deadlocks.

- Use Lock-Free Data Structures When Possible, For simple counters or flags, use **atomic classes** (e.g., `AtomicInteger`) to avoid locking overhead.

---

## **Summary**

`ReentrantReadWriteLock` is a powerful tool that allows multiple threads to read concurrently while ensuring exclusive access for writes. However, it is most effective in read-heavy scenarios. Understanding potential issues like write starvation, deadlocks, and performance degradation is essential for using this lock effectively, by following best practices like consistent lock ordering, minimizing lock duration, and monitoring lock usage, you can avoid common pitfalls and maximize the performance benefits of `ReentrantReadWriteLock`.

---