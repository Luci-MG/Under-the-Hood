# **Concurrency and Parallelism**

Both **concurrency** and **parallelism** refer to ways a computer performs multiple tasks, but they differ in how the tasks are executed. Let's go through them, along with related concepts like **threads, processes, and programs**.

---

## **What is Process ?**

A process is an instance of a running **program** (an executable code). Each process runs in isolation and gets its own memory space. eg: Opening a browser or text editor creates a process.

!!! info "Characteristics"
    - Processes do **not share memory** by default.
    - Each process has its **own address space**, file handles, and system resources.
    - **Communication** between processes is more expensive (requires Inter-Process Communication, like pipes or sockets).

---

## **What is Thread ?**

A thread is the **smallest unit of execution** within a process. Threads run **within a process** and share the same memory space. eg: A web browser might use multiple threads one for rendering pages, one for handling user input, and another for downloading files.

!!! info "Characteristics"
    - Multiple threads **within the same process** can access the same memory.
    - **Lighter** than processes since they don’t require their own memory.
    - Threads are managed and scheduled by the operating system or a thread pool.

---

## **What is Concurrency ?**
Concurrency is when multiple tasks make progress within the same time frame, but not necessarily at the same exact moment. Tasks switch back and forth, sharing resources like CPU time.

!!! info "Analogy" 
    It’s like a chef preparing multiple dishes, working on one dish for a few minutes, switching to another, and then returning to the previous dish.

!!! note
    Focuses on dealing with multiple tasks by **time-sharing** resources.

---

## **What is Parallelism ?**
Parallelism is when multiple tasks are executed simultaneously, usually on different processors or cores.

!!! info "Analogy" 
    It’s like having multiple chefs, each cooking one dish at the same time.

!!! note
    Requires multiple CPUs or cores for real simultaneous execution.

---

## **Concurrency with Threads and Processes**

!!! info "Concurrency with Threads"
    - Multiple threads are **interleaved** by the operating system to make progress.
    - On a **single-core CPU**, threads take turns using the CPU, creating the illusion of multitasking.
    - Example: A text editor might have one thread handling keystrokes and another saving your work in the background.

!!! info "Concurrency with Processes"
    - Multiple processes can run concurrently by **switching between them**.
    - Context switching between processes is more **expensive** since the OS needs to switch memory spaces and system resources.

---

## **Parallelism in Threads and Processes**

!!! info "Parallelism with Threads"
    - On a **multi-core CPU**, multiple threads can run **truly in parallel**, each on a different core.
    - Example: A parallelized program (like video rendering software) might divide the task across multiple threads running on different cores.

!!! info "Parallelism with Processes"
    - Similarly, multiple **processes** can run on multiple cores, each doing different work simultaneously.
    - Example: Running multiple instances of a program like Python interpreters on different cores.

---

## **Example Scenarios**

1. **Concurrent Programming** (e.g., in Java, Python):
    - Threads are used for **non-blocking I/O** operations, like downloading files while continuing to run the program’s main logic.
    - In **asynchronous programming** (like with `async/await` in Python), tasks switch between each other, sharing the same thread.

2. **Parallel Programming** (e.g., using Python's multiprocessing or CUDA for GPU computation):
    - Tasks are divided into multiple **processes or threads**, each of which executes on a **different core** for faster performance.
    - Used in tasks like **matrix multiplications** or **large-scale simulations** that benefit from simultaneous execution.

---

## **When to Use ?**

- **Concurrency**: Useful when you need to deal with **many tasks at once**, like handling user inputs, network requests, or database queries.
- **Parallelism**: Ideal for **computationally intensive tasks**, such as scientific simulations, video processing, or machine learning models.

---

## **Issues in Concurrency**

Below are key **issues associated with parallelism and concurrency**, categorized by type and real-world examples.
 
???+ danger "Race Conditions" 
    When two or more threads/processes try to access and modify shared data simultaneously, the final result may depend on the sequence of execution, leading to unpredictable outcomes.  

    **Example**: Two bank transactions updating the same account balance at the same time might result in lost updates.

    !!! success "Solution"
        Use synchronization mechanisms like **locks** or **mutexes** to control access to shared resources.

???+ danger "Deadlocks"  
    Occurs when two or more threads/processes block each other by holding resources and waiting for resources held by the other.  
    **Example**: Process A holds Resource 1 and waits for Resource 2, which is held by Process B, and vice versa.

    !!! success "Solution"
        Use techniques like **resource ordering**, **deadlock detection**, or **timeouts** to avoid deadlocks.

???+ danger "Livelock" 
    In livelock, threads are constantly changing states to respond to each other but never make actual progress. It’s similar to two people trying to step aside but always stepping in each other’s way.  
    **Example**: Two threads repeatedly yield control to avoid conflict, but neither progresses.

    !!! success "Solution" 
        Add randomness or back-off mechanisms to break the cycle.

???+ danger "Starvation"  
    A thread or process may be blocked indefinitely because other higher-priority tasks consume all the resources.  
    **Example**: A low-priority thread never gets CPU time because high-priority threads always take precedence.

    !!! success "Solution" 
        Use **fair scheduling** algorithms to ensure all tasks eventually get a chance to execute.

???+ danger "Context Switching Overhead"  
    Switching between multiple threads or processes incurs a cost, as the CPU saves and restores the state of each thread. Excessive context switching can reduce performance.  
    **Example**: An overloaded web server with too many threads may spend more time switching contexts than doing actual work.

    !!! success "Solution"
        Minimize the number of threads and optimize the task scheduling.

---

## **Issues in Parallelism**

???+ danger "Load Imbalance"  
    If the workload is not evenly distributed across threads or processes, some cores might remain underutilized while others are overloaded.  
    **Example**: In a matrix multiplication task, if one thread processes a large chunk and another a small chunk, the first thread might take longer, slowing down the whole task.

    !!! success "Solution"
        Use **dynamic load balancing** or **work stealing** techniques to distribute the workload effectively.

???+ danger "Scalability Bottlenecks"  
    As more threads or processes are added, the overhead of synchronization and communication increases, limiting performance improvements.  
    **Example**: A program may scale well with 4 threads but show diminishing returns with 16 threads due to synchronization overhead.

    !!! success "Solution"
        Optimize algorithms for scalability and minimize shared resources to reduce synchronization costs.

???+ danger "False Sharing"
    Occurs when multiple threads on different cores modify variables that are close in memory, leading to unnecessary cache invalidations and reduced performance.  
    **Example**: Two threads updating variables in the same cache line can cause frequent cache synchronization, slowing execution.

    !!! success "Solution"
        Align data properly in memory to avoid false sharing.

???+ danger "Communication Overhead"  
    In parallel systems, threads or processes may need to communicate with each other, which adds overhead.  
    **Example**: In distributed computing, passing messages between nodes can slow down the computation.

    !!! success "Solution"
        Reduce communication frequency or use **message batching** techniques to minimize overhead.

???+ danger "Debugging and Testing Complexity"  
    Debugging concurrent or parallel programs is harder because issues like race conditions or deadlocks may only appear under specific conditions, making them difficult to reproduce.  
    **Example**: A race condition might only occur when threads execute in a specific order, which is hard to detect in testing.

    !!! success "Solution"
        Use **debugging tools** like thread analyzers and log events to trace execution paths.

---

## **Common Issues**

???+ danger "Data Consistency and Synchronization"  
    Ensuring that shared data remains consistent when accessed by multiple threads or processes is challenging.  
    **Example**: If multiple threads increment the same counter, the final result may be incorrect without proper synchronization.

    !!! success "Solution" 
        Use **locks, semaphores**, or **atomic operations** to ensure data consistency.

???+ danger "Performance Trade-offs"  
    Parallel or concurrent execution does not always lead to better performance. In some cases, overhead from synchronization, communication, and context switching can negate performance gains.  
    **Example**: A parallel algorithm may run slower on a small dataset due to the overhead of managing multiple threads.

    !!! success "Solution"  
        Assess whether the overhead is justified and use **profiling tools** to analyze performance.

???+ danger "Non-Deterministic Behavior"  
    In concurrent and parallel systems, the order of execution is not guaranteed, leading to non-deterministic results.  
    **Example**: Running the same multi-threaded program twice may produce different outcomes, making testing and debugging difficult.

    !!! success "Solution" 
        Use **locks and barriers** carefully, and design programs to tolerate or avoid non-determinism where possible.

???+ danger "Resource Contention"  
    Threads and processes compete for shared resources, such as memory, I/O, and network bandwidth, leading to bottlenecks.  
    **Example**: Multiple processes writing to the same disk simultaneously may degrade performance.

    !!! success "Solution" 
        Optimize resource usage and avoid unnecessary contention by reducing shared resources.

---

## **Summary Table**

| **Aspect**           | **Threads**                        | **Processes**                      |
|----------------------|-------------------------------------|------------------------------------|
| **Memory Space**     | Shared within the same process      | Separate for each process         |
| **Overhead**         | Low (lightweight)                  | High (needs its own resources)    |
| **Communication**    | Easy (shared memory)               | Complex (requires IPC)            |
| **Execution**        | Within a single process            | Each process runs independently  |
| **Parallelism**      | Can run on multiple cores          | Can run on multiple cores         |

---

## **Summary**

**Concurrency** deals with **multiple tasks making progress** within the same period (may or may not be simultaneous).
**Parallelism** deals with **tasks running simultaneously** on different cores or processors.

Processes and threads are core to both concurrency and parallelism, with **threads** sharing memory within a process and **processes** running independently with isolated memory.

While concurrency and parallelism offer significant benefits, they also come with **substantial challenges**. Managing issues such as **race conditions, deadlocks, false sharing, and debugging complexity** requires thoughtful design and appropriate use of synchronization techniques. Additionally, **scalability bottlenecks** and **communication overhead** can limit the effectiveness of parallel systems. 

To mitigate these issues, some solution are:

- Choose appropriate **synchronization mechanisms**.
- Use **profiling tools** to identify bottlenecks.
- Balance the **trade-offs** between concurrency/parallelism and overhead.
- Test thoroughly under various conditions to catch **non-deterministic issues**. 

---