# **`synchronized` vs `ReentrantLock`**

## **Differences**

| **Feature**                         | **`synchronized`**                              | **`ReentrantLock`**                              |
|-------------------------------------|------------------------------------------------|------------------------------------------------|
| **Basic Concept**                   | Uses **intrinsic lock (monitor)** on objects.  | Uses an **explicit lock** from `java.util.concurrent.locks`. |
| **Lock Acquisition**                | Acquired implicitly when entering a synchronized block or method. | Acquired explicitly via `lock()` method. |
| **Release of Lock**                 | Automatically released when the thread exits the synchronized block or method. | Must be explicitly released via `unlock()`. |
| **Reentrancy**                      | Supports **reentrancy** (same thread can acquire the same lock multiple times). | Supports **reentrancy** just like synchronized. |
| **Fairness**                        | **Unfair** by default (no control over thread access order). | Can be **fair** or **unfair** (configurable with `ReentrantLock(true)`). |
| **Interruptibility**                | Cannot respond to **interrupts** while waiting for the lock. | Supports **interruptible locking** via `lockInterruptibly()`. |
| **Try Locking**                     | Not supported. A thread will **block indefinitely** if the lock is not available. | Supports **tryLock()** to attempt locking without blocking or with timeout. |
| **Condition Variables**             | Uses **`wait()` / `notify()` / `notifyAll()`** methods on the intrinsic lock. | Supports multiple **`Condition` objects** for finer-grained wait/notify control. |
| **Timeout Support**                 | Not supported. If the lock is held by another thread, it will wait indefinitely. | Supports **timeout** locking with `tryLock(long timeout, TimeUnit unit)`. |
| **Performance Overhead**            | Low for **simple scenarios** with little contention. | Higher overhead but provides **greater control** over locking behavior. |
| **Fair Locking Option**             | Not supported (always unfair).                | Fair locking can be enabled with `ReentrantLock(true)`. |
| **Use in Non-blocking Operations**  | Not possible.                                 | Possible with `tryLock()` (non-blocking). |
| **Flexibility and Control**         | Limited to synchronized methods or blocks.    | Greater flexibility: lock multiple sections, lock only part of a method, or use **multiple conditions**. |
| **Suitability for Deadlock Avoidance** | Requires external logic to prevent deadlocks (acquire locks in the same order). | Easier to prevent deadlocks using `tryLock()` and **timeouts**. |
| **Memory Usage**                    | No additional memory overhead. Uses the **object’s monitor**. | Requires additional memory for lock objects and **lock metadata**. |
| **Readability and Simplicity**      | Easier to read and maintain (especially for small, simple use cases). | More complex code with **explicit lock management**. |
| **Error Handling**                  | No need to manage lock release in a `finally` block. The lock is automatically released. | Requires **explicit unlock()** in `finally` blocks to avoid deadlocks or memory leaks. |
| **Thread Starvation**               | Prone to **thread starvation** in high contention scenarios. | Can prevent starvation using **fair lock** mode. |
| **Recommended Use Case**            | Best for **simple synchronization** needs where you don’t need advanced control. | Recommended for **complex concurrency** scenarios needing **fine-grained locking, fairness, tryLock, or interruptibility**. |

---

## **When to Use ?**

| **Use `synchronized`**                          | **Use `ReentrantLock`**                        |
|------------------------------------------------|------------------------------------------------|
| When you need simple, **block-level or method-level synchronization**. | When you need **advanced control** over locking behavior (e.g., tryLock, fairness, or interruptibility). |
| When you want **automatic lock release** (less error-prone). | When you need **multiple locks or condition variables**. |
| When **performance matters** in low-contention scenarios (lower overhead). | When dealing with **high contention** and you need **fair scheduling** to prevent starvation. |
| When you don't need **non-blocking operations** or **timeouts**. | When you want **non-blocking operations** using `tryLock()` or **timeout-based locking**. |
| When the code needs to be **simple and easy to read**. | When code complexity is acceptable for **greater flexibility**. |

---

## **Summary**

Both **`synchronized`** and **`ReentrantLock`** have their own strengths and use cases. Use `synchronized` for **simpler, lower-level concurrency needs**, and **`ReentrantLock`** when you need **more control, fairness, or advanced features** like **non-blocking locking** and **condition variables**. 

In general:
- **`synchronized`** is easier to use and less error-prone.
- **`ReentrantLock`** is more powerful and flexible, but with more overhead and complexity.

---