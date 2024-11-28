# **Thread Pool Configuration Tuning**

Thread pool configuration is critical for optimizing the performance of your applications. Poorly configured thread pools can lead to problems such as CPU starvation, thread contention, memory exhaustion, or poor resource utilization. In this Article, we’ll dive deep into CPU-bound vs I/O-bound tasks, explore how to determine optimal thread pool sizes, and discuss key considerations such as queue types and rejection policies.

---

## **CPU vs I/O Bound Tasks**

When configuring thread pools, it is essential to classify your tasks as CPU-bound or I/O-bound, as this distinction guides the number of threads your pool should maintain.

### **CPU-Bound Tasks**

Tasks that perform intensive computations (e.g., mathematical calculations, data processing, encoding), and here limiting factor is the **CPU core availability**. So its better to avoid context switching overhead by keeping the number of threads close to the available CPU cores.

```java title="Optimal Thread Pool Size for CPU-Bound Tasks"
int coreCount = Runtime.getRuntime().availableProcessors();
ExecutorService cpuBoundPool = Executors.newFixedThreadPool(coreCount);
```
!!! note
    If more threads than CPU cores are running, threads will compete for CPU cycles, causing **context switching**, which adds overhead.
    ```
    Optimal Threads = Number of Cores
    ```

???+ tip "When to use ?"
      - Data crunching (e.g., scientific calculations).
      - Image or video processing.
      - Encryption/decryption tasks.


### **I/O-Bound Tasks**

 Tasks that spend most of the time **waiting** for I/O operations (e.g., network, database, file I/O). and here the limiting factor is the time spent **waiting on I/O**. So it's better to use **more threads** than the number of cores to ensure that idle CPU cycles are used efficiently while waiting for I/O.

```java title="Optimal Thread Pool Size for I/O-Bound Tasks"
int coreCount = Runtime.getRuntime().availableProcessors();
int optimalThreads = coreCount * 2 + 1;
ExecutorService ioBoundPool = Executors.newFixedThreadPool(optimalThreads);
```
!!! note
    Since the tasks spend significant time waiting for I/O, more threads can be created to make sure the **CPU is not idle** while other threads wait for input/output operations.
    ```
    Optimal Threads = Number of Cores * (1 + Wait Time / Compute Time)
    ```

???+ tip "When to use ?"
      - Web servers handling multiple HTTP requests.
      - Database query processing.
      - File upload/download tasks.

---

## **Queues for ThreadPoolExecutor**

Choosing the right work queue is crucial for memory management and task scheduling. The queue holds **tasks waiting to be executed** when all threads are busy.

### **Unbounded Queue**
A queue with **no size limit**, but if too many tasks are submitted, it can lead to memory exhaustion (out-of-memory errors).

```java title="LinkedBlockingQueue"
BlockingQueue<Runnable> queue = new LinkedBlockingQueue<>();
```

???+ tip "When to use ?"
      Suitable only if you expect tasks to **complete quickly** and the queue will not grow indefinitely.

### **Bounded Queue**
 A queue with a **fixed size limit**, it prevents unbounded memory usage, and If the queue is full, tasks will be **rejected** or handled based on a rejection policy.

```java title="ArrayBlockingQueue"
BlockingQueue<Runnable> queue = new ArrayBlockingQueue<>(10);
```

???+ tip "When to use ?"
     Ideal for **controlled environments** where you want to cap the number of waiting tasks.

---

## **Thread Pool Size Tuning**

``` title="For CPU-Bound Tasks"
Optimal Threads = Number of Cores
```

```  title="For I/O-Bound Tasks"
Optimal Threads = Number of Cores * (1 + Wait Time / Compute Time)
```

???+ example
     If a thread spends 70% of the time waiting on I/O, and only 30% performing work:
     ```
     Optimal Threads = 4 * (1 + 0.7 / 0.3) = 12
     ```

---

## **Rejection Policies**

When the task queue is full and the pool is at its maximum size, the `ThreadPoolExecutor` must decide what to do with new tasks. You can configure rejection policies to handle these situations.

### **AbortPolicy (Default)** 
   - Throws a `RejectedExecutionException`.
   - Use when you want to fail fast upon task overload.
   ```java
   new ThreadPoolExecutor.AbortPolicy();
   ```

### **CallerRunsPolicy**
   - Executes the task in the calling thread (the thread that submitted the task).
   - Prevents task loss but slows down the caller.
   ```java
   new ThreadPoolExecutor.CallerRunsPolicy();
   ```

### **DiscardPolicy**
   - Silently discards the rejected task.
   - Use when tasks are non-critical.
   ```java
   new ThreadPoolExecutor.DiscardPolicy();
   ```

### **DiscardOldestPolicy**
   - Discards the oldest unhandled task in the queue to make room for the new task.
   - Use when newer tasks are more critical.
   ```java
   new ThreadPoolExecutor.DiscardOldestPolicy();
   ```

---

## **Monitoring Thread Pools**

Monitoring thread pools ensures that your configuration is correct and performing well. You can monitor the following metrics:

!!! note "Key Metrics to Monitor"
      - **Active Threads**: Check how many threads are actively working.
      - **Queue Size**: Monitor how many tasks are waiting in the queue.
      - **Rejected Tasks**: Track rejected tasks to see if the pool is overwhelmed.
      - **Average Task Time**: Measure how long tasks take to execute.

???+ example "Example: Monitoring Active Threads"
      ```java
      ThreadPoolExecutor executor = new ThreadPoolExecutor(2, 4, 30, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(2));

      System.out.println("Active Threads: " + executor.getActiveCount());
      System.out.println("Task Count: " + executor.getTaskCount());
      System.out.println("Completed Tasks: " + executor.getCompletedTaskCount());
      ```

---

## **Dynamic Thread Pool Adjustment**

Sometimes, you may need to adjust the pool size at runtime to respond to changing workloads.

???+ example "Example: Adjusting Thread Pool Size Dynamically"
      ```java
      ThreadPoolExecutor executor = new ThreadPoolExecutor(2, 4, 30, TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(10));

      // Adjust core and max pool size dynamically
      executor.setCorePoolSize(3);
      executor.setMaximumPoolSize(6);
      ```

---

## **Best Practices**


- For **CPU-bound tasks**, set the size close to the number of CPU cores.
```java
int poolSize = Runtime.getRuntime().availableProcessors();
ExecutorService executor = Executors.newFixedThreadPool(poolSize);
```
- For **I/O-bound tasks**, use more threads than the number of cores.

- Use bounded queues to prevent memory issues.

- Monitor and tune Continuously monitor thread pools and adjust configuration as needed.

- Gracefully shutdown thread pools to prevent resource leaks
```java
executor.shutdown();
try {
      if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
         executor.shutdownNow();
      }
} catch (InterruptedException e) {
      executor.shutdownNow();
}
```

- Use `CallerRunsPolicy` for tasks that must be executed but are non-critical (to prevent task loss).

---