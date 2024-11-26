# **Thread Pools**.

## **What is a Thread Pool ?**

A **thread pool** is a collection of worker threads that are created at the start and reused to perform multiple tasks. When tasks are submitted to the pool, a free thread picks up the task and executes it. If no threads are free, the tasks wait in a **queue** until one becomes available.

---

## **Advantages of Thread Pooling**
- Reduces the overhead of creating and destroying threads improving performence.
- Prevents overloading the system with too many threads with better resource management.
- Handles the submission of multiple concurrent tasks efficiently.
- Provides better scalability for server applications.

---

## **Creating Thread Pools**

### **Ways to Create**

- The `Executors` class provides convenient factory methods to create thread pools:

    - `newFixedThreadPool()`
    - `newCachedThreadPool()`
    - `newSingleThreadExecutor()`
    - `newScheduledThreadPool()`

- For greater control, you can instantiate `ThreadPoolExecutor` directly.

### **Fixed Thread Pool**

Creates a pool with a **fixed number of threads**. When all threads are busy, tasks are placed in a **queue** and executed as soon as a thread becomes available.

???+ example "`newFixedThreadPool`"
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class FixedThreadPoolExample {
        public static void main(String[] args) {
            ExecutorService executor = Executors.newFixedThreadPool(3);

            for (int i = 1; i <= 6; i++) {
                int taskId = i;
                executor.execute(() -> {
                    System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName());
                });
            }

            executor.shutdown();
        }
    }
    ```

???+ success "Advantages"
    - Limits the number of concurrent threads.
    - Ideal when the number of tasks is known in advance.
    - Helps avoid resource exhaustion by limiting threads.

???+ tip "When to Use ?"
    - CPU-bound tasks where the thread count is close to the number of available processors.
    - Server applications that need to serve a fixed number of requests at any given time.

---

### **Cached Thread Pool**

A **dynamic thread pool** where threads are created as needed. If threads are idle for 60 seconds, they are terminated. If a thread is available, it will be reused for a new task.

???+ example "`newCachedThreadPool`"
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class CachedThreadPoolExample {
        public static void main(String[] args) {
            ExecutorService executor = Executors.newCachedThreadPool();

            for (int i = 1; i <= 5; i++) {
                int taskId = i;
                executor.execute(() -> {
                    System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName());
                });
            }

            executor.shutdown();
        }
    }
    ```

???+ success "Advantages"
    - Highly scalable, creates threads as needed.
    - Best for short-lived, lightweight tasks.

???+ tip "When to Use ?"
    - I/O-bound tasks that spend most of the time waiting (e.g., network requests).
    - Scenarios where the number of tasks fluctuates frequently.

???+ failure "Drawbacks"
    - Can potentially **overwhelm the system** with too many threads if tasks arrive rapidly.

---

### **Single Thread Executor**

A **single-threaded executor** that ensures tasks are executed **sequentially** in the order they are submitted. If the thread dies due to an exception, a new thread is created to replace it.

???+ example "`newSingleThreadExecutor`"
    ```java
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class SingleThreadExecutorExample {
        public static void main(String[] args) {
            ExecutorService executor = Executors.newSingleThreadExecutor();

            for (int i = 1; i <= 3; i++) {
                int taskId = i;
                executor.execute(() -> {
                    System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName());
                });
            }

            executor.shutdown();
        }
    }
    ```

???+ success "Advantages"
    - Guarantees sequential execution of tasks.
    - Thread safety No need for additional synchronization between tasks.

???+ tip "When to Use ?"
    - Useful when tasks must be executed in a strict sequence (e.g., writing logs).
    - Scenarios that require single-threaded logic (e.g., managing a shared resource).

---

### **Scheduled Thread Pool**

A **scheduled thread pool** allows you to **schedule tasks** to run after a delay or periodically at a fixed rate.

???+ example "`newScheduledThreadPool`"
    ```java
    import java.util.concurrent.Executors;
    import java.util.concurrent.ScheduledExecutorService;
    import java.util.concurrent.TimeUnit;

    public class ScheduledThreadPoolExample {
        public static void main(String[] args) {
            ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

            Runnable task = () -> System.out.println("Task executed by " + Thread.currentThread().getName());

            // Schedule task to run after 3 seconds
            scheduler.schedule(task, 3, TimeUnit.SECONDS);

            // Schedule task to run repeatedly every 2 seconds
            scheduler.scheduleAtFixedRate(task, 1, 2, TimeUnit.SECONDS);

            // Allow the tasks to complete after 10 seconds
            scheduler.schedule(() -> scheduler.shutdown(), 10, TimeUnit.SECONDS);
        }
    }
    ```

???+ success "Advantages"
    - Delayed and periodic execution of tasks.
    - Ideal for timing-sensitive operations.

???+ tip "When to Use ?"
    - Polling services or periodic background tasks (e.g., refreshing a cache).
    - Scheduled events, like sending notifications at intervals.

---

### **ThreadPoolExecutor**

`ThreadPoolExecutor` is the **core implementation** of thread pools in Java. Using it allows you to **fine-tune** the thread pool’s behavior with more control over the number of threads, queue type, and rejection policy.

```java title="Parameters of ThreadPoolExecutor"
ThreadPoolExecutor executor = new ThreadPoolExecutor(
        corePoolSize,      // Minimum number of threads
        maximumPoolSize,   // Maximum number of threads
        keepAliveTime,     // Idle time before a thread is terminated
        timeUnit,          // Time unit for keepAliveTime
        workQueue,         // Queue to hold waiting tasks
        threadFactory,     // Factory to create new threads
        handler            // Rejection policy when the queue is full
);
```

???+ example "Custom Thread Pool"
    ```java
    import java.util.concurrent.*;

    public class CustomThreadPoolExecutorExample {
        public static void main(String[] args) {
            ThreadPoolExecutor executor = new ThreadPoolExecutor(
                    2, 4, 30, TimeUnit.SECONDS,
                    new LinkedBlockingQueue<>(2),   // Task queue with capacity 2
                    Executors.defaultThreadFactory(),
                    new ThreadPoolExecutor.CallerRunsPolicy() // Rejection policy
            );

            // Submit 6 tasks to the pool
            for (int i = 1; i <= 6; i++) {
                int taskId = i;
                executor.execute(() -> {
                    System.out.println("Task " + taskId + " executed by " + Thread.currentThread().getName());
                });
            }

            executor.shutdown();
        }
    }
    ```

???+ success "Advantages"
    - Fine-tuned control over thread management.
    - Allows using custom queues and rejection policies.

???+ tip "When to Use ?"
    - Applications with complex task management needs.
    - Systems where you need to monitor and control thread behavior closely.


**Common Rejection Policies** in `ThreadPoolExecutor`

- **AbortPolicy**: Throws `RejectedExecutionException` when a task is rejected.
- **CallerRunsPolicy**: Executes the rejected task in the caller's thread.
- **DiscardPolicy**: Silently discards the rejected task.
- **DiscardOldestPolicy**: Discards the oldest unhandled task.

---

## **Comparison**

| **Thread Pool Type**        | **Concurrency** | **Parallelism** | **Task Type**             | **When to Use**                   |
|-----------------------------|----------------|----------------|--------------------------|-----------------------------------|
| Fixed Thread Pool           | Yes            | Yes            | Long-running tasks       | Limited number of known tasks.   |
| Cached Thread Pool          | Yes            | Yes            | Short-lived tasks        | Dynamic workloads with many I/O tasks. |
| Single Thread Executor      | No             | No             | Sequential tasks         | Strictly ordered execution.      |
| Scheduled Thread Pool       | Yes            | Yes            | Timed or periodic tasks  | Periodic background tasks.       |
| Custom ThreadPoolExecutor   | Yes            | Yes            | Mixed                    | Advanced control and tuning.     |

---

## **Interface Concepts**

### **Runnable Interface**

The `Runnable` interface represents a **task** that can run asynchronously in a thread but **does not return any result** or throw a checked exception.

```java title="Structure"
@FunctionalInterface
public interface Runnable {
    void run();
}
```

???+ example
    ```java
    public class RunnableExample {
        public static void main(String[] args) {
            Runnable task = () -> {
                System.out.println("Executing task in: " + Thread.currentThread().getName());
            };

            Thread thread = new Thread(task);
            thread.start();
        }
    }
    ```

???+ tip "When to Use ?"
    - Use `Runnable` when **no result** is expected from the task.
    - Commonly used to run simple background tasks.


### **Callable Interface**

The `Callable` interface is similar to `Runnable`, but it **can return a result** and **throw a checked exception**.

```java title="Structure"
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

???+ example
    ```java
    import java.util.concurrent.Callable;

    public class CallableExample {
        public static void main(String[] args) throws Exception {
            Callable<Integer> task = () -> {
                System.out.println("Executing task in: " + Thread.currentThread().getName());
                return 42;
            };

            // Direct call (for demonstration)
            Integer result = task.call();
            System.out.println("Task result: " + result);
        }
    }
    ```

???+ tip "When to Use ?"
    - Use `Callable` when a **result** or an **exception** is expected.
    - Works well with thread pools where tasks need to return values (e.g., for **parallel computation**).


### **Future Interface**

A `Future` represents the **result of an asynchronous computation**. It provides methods to check if the computation is complete, wait for the result, and cancel the task if necessary.

```java title="Structure"
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

???+ example
    ```java
    import java.util.concurrent.*;

    public class FutureExample {
        public static void main(String[] args) throws ExecutionException, InterruptedException {
            ExecutorService executor = Executors.newSingleThreadExecutor();

            Callable<Integer> task = () -> {
                Thread.sleep(2000); // Simulate some work
                return 42;
            };

            Future<Integer> future = executor.submit(task);

            // Do something else while the task executes asynchronously
            System.out.println("Task is running...");

            // Wait for the result
            Integer result = future.get();
            System.out.println("Task result: " + result);

            executor.shutdown();
        }
    }
    ```

???+ tip "When to Use ?"
    - **`Future`** allows you to **submit tasks** to thread pools and **retrieve their results** once completed.
    - Useful for **waiting for multiple tasks** to finish.

???+ note "Key Methods"
    - **`get()`**: Blocks until the task completes and returns the result.
    - **`isDone()`**: Checks if the task is completed.
    - **`cancel()`**: Cancels the task if it's still running.


### **BlockingQueue Interface**

`BlockingQueue` is a **thread-safe queue** that **blocks the calling thread** when:

- **Retrieving from an empty queue**: The thread waits until an item becomes available.
- **Adding to a full queue**: The thread waits until space is available.

```java title="Structure"
public interface BlockingQueue<E> extends Queue<E> {
    void put(E e) throws InterruptedException;
    E take() throws InterruptedException;
    // Other methods for timed operations, size, etc.
}
```

???+ example
    ```java
    import java.util.concurrent.*;

    public class BlockingQueueExample {
        public static void main(String[] args) {
            BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(2);

            // Producer thread
            new Thread(() -> {
                try {
                    queue.put(1);
                    System.out.println("Added 1 to the queue");
                    queue.put(2);
                    System.out.println("Added 2 to the queue");
                    queue.put(3); // This will block until space is available
                    System.out.println("Added 3 to the queue");
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }).start();

            // Consumer thread
            new Thread(() -> {
                try {
                    Thread.sleep(1000); // Simulate some delay
                    System.out.println("Removed from queue: " + queue.take());
                    System.out.println("Removed from queue: " + queue.take());
                    System.out.println("Removed from queue: " + queue.take());
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }).start();
        }
    }
    ```


???+ tip "Usages ?"
    - `BlockingQueue` is commonly used for task queues in thread pools (`ThreadPoolExecutor`).
    - Ensures proper handoff between producers and consumers without explicit synchronization.

!!! note "Types of BlockingQueues"
    - **`ArrayBlockingQueue`**: A fixed-size queue.
    - **`LinkedBlockingQueue`**: A potentially unbounded queue.
    - **`PriorityBlockingQueue`**: A priority-based queue.


### **Runnable vs Callable**

| **Aspect**           | **Runnable**                             | **Callable**                              |
|----------------------|--------------------------------------------|-------------------------------------------|
| **Result**           | No result                                  | Returns a result                         |
| **Exception Handling** | Cannot throw checked exceptions          | Can throw checked exceptions             |
| **Functional Interface** | Yes (`run()` method)                    | Yes (`call()` method)                    |
| **Use Case**         | Simple background tasks                    | Tasks that need to return a value or throw an exception |

### **How These Work Together**

```java title="Using Runnable in a Thread Pool"
ExecutorService executor = Executors.newFixedThreadPool(2);
Runnable task = () -> System.out.println("Task executed by " + Thread.currentThread().getName());
executor.execute(task);
executor.shutdown();
```

```java title="Using Callable with Future in a Thread Pool"
ExecutorService executor = Executors.newFixedThreadPool(2);
Callable<Integer> task = () -> 42;
Future<Integer> future = executor.submit(task);
System.out.println("Result: " + future.get());
executor.shutdown();
```

```java title="Using BlockingQueue with ThreadPoolExecutor"
BlockingQueue<Runnable> queue = new LinkedBlockingQueue<>(2);
ThreadPoolExecutor executor = new ThreadPoolExecutor(2, 4, 30, TimeUnit.SECONDS, queue);
Runnable task = () -> System.out.println("Task executed by " + Thread.currentThread().getName());
executor.execute(task);
executor.shutdown();
```

---