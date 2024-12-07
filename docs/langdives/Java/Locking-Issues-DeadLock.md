# **Issues with Locking - DeadLock**

Locking mechanisms in Java, while essential for ensuring thread safety in multithreaded applications, can introduce various issues if not used properly. 

In this article, we’ll explore how deadlocks occur, how to prevent them, and practical examples of various techniques to detect and resolve deadlocks. A **deadlock** is a common concurrency issue in multithreaded programs and can severely impact performance.

---

## **What is Deadlock ?**

A deadlock occurs when two or more threads are blocked indefinitely, Each thread is waiting for a lock held by the other, and neither can proceed.

This results in a circular wait, where no thread can release the locks it holds, leading to a deadlock condition.

---

## **How Deadlock Occurs ?**

Let’s revisit the classic **deadlock example**.

???+ example "Deadlock Example"

    ```java
    class A {
        public synchronized void methodA(B b) {
            System.out.println(Thread.currentThread().getName() + ": Locked A, waiting for B...");
            try {
                Thread.sleep(50);  // Simulate some work
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            b.last();  // Waiting for lock on object B
        }

        public synchronized void last() {
            System.out.println(Thread.currentThread().getName() + ": Inside A.last()");
        }
    }

    class B {
        public synchronized void methodB(A a) {
            System.out.println(Thread.currentThread().getName() + ": Locked B, waiting for A...");
            try {
                Thread.sleep(50);  // Simulate some work
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            a.last();  // Waiting for lock on object A
        }

        public synchronized void last() {
            System.out.println(Thread.currentThread().getName() + ": Inside B.last()");
        }
    }

    public class DeadlockDemo {
        public static void main(String[] args) {
            A a = new A();
            B b = new B();

            Thread t1 = new Thread(() -> a.methodA(b), "Thread 1");
            Thread t2 = new Thread(() -> b.methodB(a), "Thread 2");

            t1.start();
            t2.start();
        }
    }
    ```

    !!! info "Flow Analysis"

        1. Thread 1 starts and calls `a.methodA(b)`. It acquires the lock on object `A` and prints:  
        ```
        Thread 1: Locked A, waiting for B...
        ```

        2. Thread 2 starts and calls `b.methodB(a)`. It acquires the lock on object `B` and prints:  
        ```
        Thread 2: Locked B, waiting for A...
        ```

        3. Now:
            - Thread 1 holds the lock on `A` and waits for Thread 2 to release the lock on `B`.
            - Thread 2 holds the lock on `B` and waits for Thread 1 to release the lock on `A`.

    Both threads are waiting indefinitely, resulting in a deadlock.

---

## **How to Avoid ?**

- Acquiring Locks in a Consistent Order
- Using `tryLock()` with Timeout
- Avoid Nested Locks
- Using Lock Ordering Techniques

### **Acquiring Locks in a Order**

If all threads acquire locks in the same order, deadlock can be prevented.

???+ example "Modified Example: Acquiring Locks in the Same Order"
    ```java 
    class A {
        public void methodA(B b) {
            synchronized (this) {
                System.out.println(Thread.currentThread().getName() + ": Locked A, waiting for B...");
                synchronized (b) {
                    System.out.println(Thread.currentThread().getName() + ": Acquired lock on B");
                    b.last();
                }
            }
        }

        public void last() {
            System.out.println(Thread.currentThread().getName() + ": Inside A.last()");
        }
    }

    class B {
        public void methodB(A a) {
            synchronized (this) {
                System.out.println(Thread.currentThread().getName() + ": Locked B, waiting for A...");
                synchronized (a) {
                    System.out.println(Thread.currentThread().getName() + ": Acquired lock on A");
                    a.last();
                }
            }
        }

        public void last() {
            System.out.println(Thread.currentThread().getName() + ": Inside B.last()");
        }
    }

    public class DeadlockResolved {
        public static void main(String[] args) {
            A a = new A();
            B b = new B();

            Thread t1 = new Thread(() -> a.methodA(b), "Thread 1");
            Thread t2 = new Thread(() -> b.methodB(a), "Thread 2");

            t1.start();
            t2.start();
        }
    }
    ```

    !!! info "Explanation"
        Both threads now acquire locks in the same order (`A` → `B`). This ensures that deadlock cannot occur.

### **Using `tryLock()` with Timeout**

The `tryLock()` method attempts to acquire a lock and **fails gracefully** if the lock is not available within a specified time.


???+ example "Deadlock Prevention using `tryLock()` example"
    ```java
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.locks.ReentrantLock;

    public class TryLockDemo {
        private final ReentrantLock lockA = new ReentrantLock();
        private final ReentrantLock lockB = new ReentrantLock();

        public void methodA() {
            try {
                if (lockA.tryLock(1, TimeUnit.SECONDS)) {
                    System.out.println(Thread.currentThread().getName() + ": Locked A");
                    Thread.sleep(50);  // Simulate some work

                    if (lockB.tryLock(1, TimeUnit.SECONDS)) {
                        try {
                            System.out.println(Thread.currentThread().getName() + ": Locked B");
                        } finally {
                            lockB.unlock();
                        }
                    } else {
                        System.out.println(Thread.currentThread().getName() + ": Could not acquire lock B, releasing A");
                    }

                    lockA.unlock();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        public void methodB() {
            try {
                if (lockB.tryLock(1, TimeUnit.SECONDS)) {
                    System.out.println(Thread.currentThread().getName() + ": Locked B");
                    Thread.sleep(50);  // Simulate some work

                    if (lockA.tryLock(1, TimeUnit.SECONDS)) {
                        try {
                            System.out.println(Thread.currentThread().getName() + ": Locked A");
                        } finally {
                            lockA.unlock();
                        }
                    } else {
                        System.out.println(Thread.currentThread().getName() + ": Could not acquire lock A, releasing B");
                    }

                    lockB.unlock();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        public static void main(String[] args) {
            TryLockDemo demo = new TryLockDemo();

            Thread t1 = new Thread(demo::methodA, "Thread 1");
            Thread t2 = new Thread(demo::methodB, "Thread 2");

            t1.start();
            t2.start();
        }
    }
    ```
    !!! info "Explanation"
        If a thread **fails to acquire a lock** within the timeout, it **releases any locks** it holds, avoiding a deadlock.

---

## **Detecting Using Monitoring Tools**

You can detect deadlocks using tools like:

- VisualVM: A monitoring tool bundled with the JDK.
- JConsole: Also part of the JDK, useful for tracking deadlocks in running applications.

---

## **Best Practices**

- Use `tryLock()` with timeout to avoid indefinite blocking.
- Minimize nested locks to reduce the chances of deadlock.
- Acquire locks in a consistent order across all threads.
- Use lock-free data structures like `AtomicInteger` or `ConcurrentHashMap` when possible.
- Analyze your code for potential deadlock scenarios.

---

## **Summary**

Deadlocks are one of the most common and dangerous issues in multithreaded programming.

---