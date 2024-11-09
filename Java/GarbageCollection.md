# **Garbage Collection**

Garbage collection (GC) in Java is essential to automatic memory management, ensuring that objects no longer needed by an application are reclaimed, and the memory they occupied is freed. This allows Java developers to avoid memory leaks and other resource-management issues.

---

## **1. Basics of Garbage Collection in Java**

1. **Managed Memory in Java:**
   - **Heap Memory:** Divided into several areas, mainly:
     - **Young Generation:** Where new objects are created.
     - **Old Generation (Tenured):** Stores objects that survived multiple GC cycles.
     - **Metaspace:** Stores class metadata (since Java 8, replaces the old **PermGen**).

2. **How GC Works:**
   - GC identifies objects that are no longer reachable from the program.
   - References from the **roots** (like static variables, thread-local variables, or stack variables) determine reachability.
   - If an object is not reachable by any live references, it becomes eligible for collection.

---

## **2. Phases of Garbage Collection**

1. **Mark Phase:** The GC algorithm starts by marking all reachable objects from the GC roots.

2. **Sweep Phase:** All unmarked objects are cleared, and their memory is reclaimed.

3. **Compact Phase (optional):** Some collectors perform heap compaction to defragment memory and avoid fragmentation issues.

---

## **3. Types of Garbage Collectors in Java**

1. **Serial Garbage Collector:**
   - **Use case:** Single-threaded environments (small applications).
   - **Working:** It uses a single thread for both young and old generations, freezing all application threads during collection (stop-the-world).

2. **Parallel Garbage Collector (Throughput Collector):**
   - **Use case:** Multi-threaded environments requiring high throughput.
   - **Working:** Uses multiple threads to perform GC in the young generation; can freeze the application threads briefly.

3. **CMS (Concurrent Mark-Sweep) Collector:** *(Deprecated since JDK 9)*
   - **Use case:** Low-latency applications.
   - **Working:** Performs marking and sweeping concurrently with application threads, reducing pause times but susceptible to fragmentation.

4. **G1 Garbage Collector (Garbage-First):**
   - **Use case:** Replaces CMS in JDK 9+. Suitable for large heaps.
   - **Working:** Divides the heap into regions, focusing on areas with the most garbage first. Offers predictable pause times.

5. **Z Garbage Collector (ZGC):**
   - **Use case:** Ultra-low pause times for very large heaps (multi-TB).
   - **Working:** Uses region-based collection and performs almost all work concurrently. Pause times remain in the low millisecond range (independent of heap size).

6. **Shenandoah GC:**
   - **Use case:** Large heap, low-latency applications.
   - **Working:** Similar to ZGC but with even more aggressive concurrent operations to minimize stop-the-world pauses.

### **Comparing Java Garbage Collectors**

| **Collector**  | **Use Case**                  | **Pause Time** | **Heap Size**     | **Parallelism**     |
|----------------|--------------------------------|----------------|------------------|---------------------|
| Serial         | Small apps, single-threaded   | High           | Small (<1 GB)    | Single-threaded     |
| Parallel       | Throughput-heavy apps         | Moderate       | Medium to large  | Multi-threaded      |
| CMS            | Low-latency (deprecated)      | Low            | Medium to large  | Concurrent          |
| G1             | Balanced throughput & latency | Predictable    | Large            | Mixed               |
| ZGC            | Ultra-low latency, huge heaps | Sub-millisecond| Multi-TB         | Highly concurrent   |
| Shenandoah     | Latency-sensitive, large heaps| Sub-millisecond| Multi-TB         | Highly concurrent   |

ZGC and Shenandoah use advanced algorithms that perform **incremental marking, remapping, and concurrent sweeping**. They avoid long pauses by offloading most GC work to concurrent threads.

---

## **4. Generational Garbage Collection In Depth**

Java heap is divided into:
- **Young Generation:** 
  - Divided into **Eden Space** and **Survivor Spaces (S0, S1)**.
  - New objects are created in Eden. After surviving a GC cycle, they move to a survivor space, and after multiple cycles, to the Old Generation.
  
- **Old Generation (Tenured):**
  - Contains long-lived objects.

**Garbage collection types:**
- **Minor GC:** Cleans the young generation.
- **Major GC:** Cleans the old generation.
- **Full GC:** Cleans both generations and compacts the heap.

### **4.1. Advanced Memory Layout**

1. **Heap Structure and Region-Based Management (G1/ZGC)**:
   - **G1 GC** divides the heap into multiple **equal-sized regions**. These regions are not contiguous but logically organized into young/old regions.
   - In **ZGC and Shenandoah**, these regions are even smaller to improve parallelism and reduce pause times.

   **Dynamic Region Management:**  
   - G1 dynamically adjusts the size and number of regions based on application memory behavior.
   - **ZGC** uses regions independently to ensure ultra-low pause times, as each GC thread works on separate regions without blocking others.

2. **Compressed Oops** (Ordinary Object Pointers):
   - Java uses **compressed pointers** (32-bit references) for heaps smaller than 32GB to optimize memory. Without this, object references would take 64 bits.
   - Enabled with:
     ```bash
     -XX:+UseCompressedOops
     ```

### **4.2. Internals: Safepoints and Stop-the-World (STW) Events**

1. **Safepoints:**
   - Java threads stop only at specific **safepoints** for GC (or other JVM activities). A **safepoint** is a checkpoint where all threads must reach before GC can start.
   - Examples: Executing bytecode that triggers allocation failure, method calls, or back branches in loops.

2. **Safepoint Polling:**
   - The JVM injects safepoint polls within running code to ensure threads hit these safepoints regularly.
   - Too many safepoint pauses indicate **GC tuning issues** or excessive thread blocking.

3. **Safepoint Latency:** 
   - JVM may delay triggering GC due to waiting for all threads to reach a safepoint, which introduces unpredictable latency. This is critical in **low-latency systems** like trading applications.

### **4.3. Write Barriers, Card Tables and Memory Fences**

1. **Write Barriers:**
   - Write barriers are small pieces of code inserted into memory writes to track references between **young** and **old generations**.
   - These ensure that changes to references are correctly accounted for during GC (especially relevant for concurrent collectors).

2. **Card Tables:** 
   - Java divides heap memory into **"cards"**—small blocks of 512 bytes. If an old object references a young object, the corresponding **card is marked dirty**.
   - During GC, only **dirty cards** are scanned instead of the entire heap, improving GC performance. This is especially relevant for **CMS** and **G1GC**.

3. **Memory Fences:**  
   - Concurrent collectors use **memory fences** to ensure visibility across threads.
   - ZGC leverages **colored pointers** to track object states (marked, remapped) without blocking threads.
---

## **5. Hierarchy of Garbage Collection Workflow**

### **5.1. Allocation of Objects**
1. **New Object Creation**:
   - Objects are first allocated in the **Eden Space** of the **Young Generation**.
   - If there’s no space in Eden, a **Minor GC** is triggered.

2. **Minor GC (Young Generation Collection)**:
   - Scans **Eden and Survivor Spaces** to collect dead objects.
   - **Live objects** are moved to **Survivor Space S1**.
   - If objects survive several Minor GCs, they are **promoted** to the **Old Generation**.

### **5.2. Thresholds and Promotions**
1. **Max Tenuring Threshold**:
   - Controls how many GC cycles an object must survive in the young generation to be promoted to the old generation.

2. **Promotion Failures**:
   - If the **Old Generation** is full or unable to accommodate promoted objects, a **Full GC** (stop-the-world) may occur.

### **5.3. Major GC and Old Generation Collection**
1. **When Old Generation Fills Up**:
   - Major GC (or **Old Generation Collection**) occurs.
   - If old generation runs out of space, it may **trigger a Full GC** or throw an **OutOfMemoryError** (OOM).

2. **Concurrent Collectors (CMS, G1, ZGC, Shenandoah)**:
   - These collectors minimize the impact of major GC by **concurrently marking and sweeping** objects with application threads running.


### **5.4. Full GC (Stop-the-World Event)**
1. **What Causes Full GC?**:
   - **Heap fragmentation** (in CMS) or **promotions failing** from young to old generation.
   - **Metaspace OutOfMemoryError** (if class metadata fills the Metaspace).

2. **What Happens During Full GC?**
   - JVM **pauses all application threads** (STW).
   - Performs **marking, sweeping, and compacting** on both young and old generations.
   - **Heap is defragmented** to ensure that future allocations don't face fragmentation.

### **5.5. Safepoints and Write Barriers**
1. **Safepoints**:
   - GC waits for all threads to hit a safepoint before starting.
   - The JVM injects **polls** in code to ensure that all threads eventually reach a safepoint.

2. **Write Barriers**:
   - Used to **track reference changes** between objects in the young and old generations.
   - Ensures correct behavior when marking reachable objects during concurrent collections.

### **5.6. Finalization and Reference Types**
1. **Soft, Weak, and Phantom References**:
   - Java handles objects with special reference types differently during GC.
   - **Soft References**: Collected when memory is low.
   - **Weak References**: Always collected during GC if not strongly reachable.
   - **Phantom References**: Used to schedule cleanup tasks for objects.

---

## **6. GC Flow Structure**:
1. **Object Creation**
   - Allocated in **Eden Space** (Young Gen).

2. **Eden Full → Trigger Minor GC**:
   - Mark live objects and move them to **Survivor Spaces**.
   - Objects surviving multiple cycles move to the **Old Generation**.

3. **Old Gen Full → Trigger Major GC**:
   - Mark and sweep objects in the Old Gen.
   - If heap is fragmented, trigger **Full GC**.

4. **Concurrent Collections** (G1, ZGC):
   - Perform **marking and sweeping concurrently** without stopping the world.

5. **Full GC (Stop-the-World)**:
   - When all else fails, **Full GC** freezes all threads, marks, sweeps, and compacts memory.

---

## 7. **Fragmentation** 

In the context of memory management refers to the inefficient use of memory that occurs when free memory is split into small, non-contiguous blocks, making it difficult to allocate larger contiguous blocks even if the total free memory is sufficient. In Java, fragmentation can occur in both the young and old generations of the heap.

### **7.1 Types of Fragmentation**

1. **Internal Fragmentation**:
   - Occurs when a block of memory is larger than what is actually needed. For example, if an object requires 10 bytes but is allocated a 16-byte block, the remaining 6 bytes are wasted.

2. **External Fragmentation**:
   - Happens when free memory is scattered in small chunks across the heap. This can lead to a situation where there isn’t enough contiguous space available to fulfill a large allocation request, even if the total free memory is sufficient.

### **7.2 Causes of Fragmentation in Java**

1. **Object Lifetimes**:
   - Short-lived objects are frequently allocated and deallocated, especially in the young generation. This can create gaps in memory as these objects are collected, leading to external fragmentation.

2. **Promotion of Objects**:
   - When objects in the young generation are promoted to the old generation, if the old generation is already fragmented, it may become difficult to allocate new objects.

3. **Full GCs**:
   - In collectors like CMS (Concurrent Mark-Sweep), memory is reclaimed but not compacted, leaving fragmented free spaces.

### **7.3 Effects of Fragmentation**

- **OutOfMemoryError**:
  - Fragmentation can cause allocation failures, leading to `OutOfMemoryError` if there isn’t enough contiguous memory available for new object allocations.

- **Increased GC Overhead**:
  - The JVM may spend more time during GC cycles trying to find suitable spaces for object allocation, which can degrade performance.

- **Heap Fragmentation:**
   - Some collectors (like CMS) suffer from **heap fragmentation** since they don’t compact memory after reclaiming space.
   - **Symptoms:** Increased GC frequency or Full GCs despite available heap space.
   - Solution: Use **G1GC** or **ZGC** which support **region-based compaction**.

- **Pinned Objects:** 
   - Sometimes, objects cannot be moved during GC (e.g., JNI references or thread-local objects). This can lead to **fragmentation**.

### **7.4 Mitigating Fragmentation**

1. **Using G1 GC or ZGC**: These collectors are designed to handle fragmentation better than older collectors. They manage memory in regions and perform compaction as part of their regular operations.

2. **Heap Size Adjustments**: Increasing the size of the old generation can help reduce the frequency of fragmentation issues.

3. **Monitoring and Tuning**: Regularly monitor memory usage and GC logs to identify fragmentation patterns. Tuning the JVM parameters can help alleviate fragmentation issues.

4. **Object Pooling**: Reusing objects instead of frequently allocating and deallocating them can help reduce fragmentation.

---

## **8.Configuring garbage collection (GC)**

Configuring garbage collection (GC) and its parameters in Java is primarily done through JVM (Java Virtual Machine) options when starting your application.

### **8.1. How to Configure GC Parameters**

1. **Command-Line Options**:
   - You can specify GC options when you start your Java application using the `java` command.
   - Example:
     ```bash
     java -Xms512m -Xmx4g -XX:+UseG1GC -XX:MaxGCPauseMillis=100 -jar your-application.jar
     ```
   - In this example:
     - `-Xms512m`: Sets the initial heap size to 512 MB.
     - `-Xmx4g`: Sets the maximum heap size to 4 GB.
     - `-XX:+UseG1GC`: Uses the G1 Garbage Collector.
     - `-XX:MaxGCPauseMillis=100`: Aims for a maximum pause time of 100 milliseconds.

2. **Environment Variables**:
   - For containerized applications (like those running in Docker or Kubernetes), you can set JVM options through environment variables or directly in the configuration file.
   - Example in Docker:
     ```dockerfile
     ENV JAVA_OPTS="-Xms512m -Xmx4g -XX:+UseG1GC"
     CMD java $JAVA_OPTS -jar your-application.jar
     ```

3. **Configuration Files**:
   - Some applications allow you to specify JVM options in a configuration file, which can be helpful for managing multiple parameters in one place.

### **8.2. Common GC Options**

1. **Basic Heap Size Configuration**:
   - `-Xms<size>`: Sets the initial heap size.
   - `-Xmx<size>`: Sets the maximum heap size.

2. **Choosing a Garbage Collector**:
   - **G1 GC**: 
     ```bash
     -XX:+UseG1GC
     ```
   - **Parallel GC**: 
     ```bash
     -XX:+UseParallelGC
     ```
   - **CMS** (Concurrent Mark-Sweep): 
     ```bash
     -XX:+UseConcMarkSweepGC
     ```
   - **ZGC**: 
     ```bash
     -XX:+UseZGC
     ```
   - **Shenandoah**: 
     ```bash
     -XX:+UseShenandoahGC
     ```

3. **Tuning G1 GC**:
   - `-XX:MaxGCPauseMillis=<time>`: Target maximum pause time.
   - `-XX:G1HeapRegionSize=<size>`: Size of the regions in G1.
   - `-XX:G1ReservePercent=<percentage>`: Percentage of heap reserved for G1 to avoid Full GC.

4. **Tuning Parallel GC**:
   - `-XX:ParallelGCThreads=<number>`: Number of threads to use for parallel GC.
   - `-XX:ConcGCThreads=<number>`: Number of concurrent threads during GC.

5. **Monitoring and Logging**:
   - `-Xlog:gc*:file=gc.log`: Logs detailed GC information to `gc.log`.
   - `-XX:+PrintGCDetails`: Outputs detailed information about each GC event.

6. **Controlling Object Promotion**:
   - `-XX:MaxTenuringThreshold=<N>`: Number of times an object can be copied in the young generation before being promoted to the old generation.

7. **Metaspace Configuration** (for class metadata):
   - `-XX:MetaspaceSize=<size>`: Initial size of Metaspace.
   - `-XX:MaxMetaspaceSize=<size>`: Maximum size of Metaspace.

### **8.3. Example Configuration**

Here’s an example of a command to start a Java application with G1 GC and some tuning parameters:

```bash
java -Xms1g -Xmx8g -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=200 \
     -XX:G1HeapRegionSize=16m \
     -XX:InitiatingHeapOccupancyPercent=30 \
     -XX:ConcGCThreads=2 \
     -Xlog:gc*:file=gc.log \
     -jar your-application.jar
```

---

## **9. Deep Tuning Techniques (GC)**

1. **Heap Size and GC Frequency:**
   - Larger heaps reduce the frequency of GC but increase GC pause times.
   - Too small a heap size leads to frequent GC, causing poor performance.

1. **GC Latency and Response Times:**
   - GC pauses affect **99th percentile latencies**, especially in real-time systems.
   - **ZGC** and **Shenandoah** are designed to keep pause times sub-millisecond by performing most operations concurrently.

1. **Application Throughput vs. Latency:**
   - Throughput-oriented collectors (like Parallel GC) achieve high throughput but at the cost of longer pauses.
   - **G1GC** offers a compromise between throughput and latency.

1. **Survivor Space Tuning:**
   - Adjust with:
     ```bash
     -XX:SurvivorRatio=<N>
     ```
     A higher ratio gives more space to Eden, and smaller Survivor spaces.

1. **Tuning GC Thresholds:**
   - **Survivor Spaces Tuning:** Adjust how long objects stay in survivor spaces using:
     ```bash
     -XX:MaxTenuringThreshold=<N>
     ```
     A higher threshold means more objects are kept in the young generation, but it may increase **Minor GC** times.

   - **Promotion Failures:** When young generation cannot fit objects into old generation (usually during high allocation bursts), GC falls back to **Full GC**. To reduce this:
     - Increase old generation size or set higher **SurvivorRatio**.

1. **Tuning G1 GC:**
   - Focus on pause times:
     ```bash
     -XX:MaxGCPauseMillis=100
     -XX:G1ReservePercent=10  // Extra heap reserved to avoid Full GC
     ```
   - Analyze **G1 Humongous Objects**: Objects larger than **half the region size** are treated as "humongous" and allocated directly in the old generation.
     ```bash
     -XX:G1HeapRegionSize=<N>  // Larger region size helps handle humongous objects efficiently
     ```

1. **Young GC Tuning:**
   - Monitor **Minor GC** behavior using:
     ```bash
     -Xlog:gc*:file=gc.log
     ```
   - Frequent Minor GC indicates:
     - Too small **Eden space**.
     - Objects are prematurely promoted to old generation, leading to **promotion failures**.

1. **Tuning Java GC for High Performance**
   - **Choose the Right GC:**  
      - Use **ZGC** or **Shenandoah** for low-latency, large-memory applications.
      - Stick with **G1GC** for general-purpose applications needing a balance between throughput and latency.

   - **Analyze and Tune:**  
      - Regularly monitor GC logs and fine-tune heap settings.
      - Avoid unnecessary Full GCs by managing object lifetimes and heap fragmentation.

1. **Handling Multi-Terabyte Heaps:**
   - ZGC and Shenandoah are designed to handle **multi-TB heaps** with minimal latency impact.
   - Even with **multi-GB objects**, pause times remain low by processing in **parallel regions**.

1. **GC in Cloud and Microservices:**
   - In **Kubernetes** environments, GC settings must align with container limits:
     ```bash
     -XX:MaxRAMPercentage=75.0
     ```
   - Use **G1GC** for microservices to ensure predictable pauses.

1. **Latency Monitoring:**
   - Monitor **GC pause impact** on response times using:
     ```bash
     -Xlog:gc*:file=gc.log
     ```
   - Look for **long Minor GC pauses** and tune accordingly.

---


## **10. Tools for GC Analysis**

1. **GC Logs:** Capture GC details by adding `-Xlog:gc*` or `-XX:+PrintGCDetails` JVM options.
   - **Sample GC Log:**
      ```
      [GC (Allocation Failure) [PSYoungGen: 2048K->512K(2560K)] 4096K->2048K(8192K), 0.0103451 secs]
      ```
      - **[GC (Allocation Failure)]:** Reason for GC.
      - **[PSYoungGen: 2048K->512K(2560K)]:** Young generation usage before and after GC.
      - **4096K->2048K(8192K):** Heap usage before and after GC.
      - **0.0103451 secs:** GC pause duration.
2. **VisualVM:** A monitoring tool bundled with the JDK for real-time JVM performance monitoring.
3. **Java Flight Recorder (JFR):** An advanced profiling tool that collects detailed JVM metrics, including GC data.
4. **JConsole:** Visualize JVM statistics and monitor heap usage.

---

## **11. Diagnosing and Troubleshooting Memory Leaks**

1. **OutOfMemoryError (OOM):**
   - Common causes:
     - Memory leak due to uncollected objects.
     - Survivor space overflow or **promotion failures**.
     - JNI leaks or unmanaged native memory usage.

2. **Heap Dump Analysis:**
   - Use tools like **jmap** to capture heap dumps:
     ```bash
     jmap -dump:format=b,file=heapdump.hprof <pid>
     ```
   - Analyze with **Eclipse MAT** (Memory Analyzer Tool) or **VisualVM**.

3. **Detecting Leaks:**
   - Look for large, unreachable objects with static references or growing collections (e.g., large `HashMap` or `ArrayList`).

4. **Java Flight Recorder (JFR):**
   - JFR provides detailed memory profiling without heavy overhead. Collect a recording and analyze it for object lifetimes, GC events, and thread behavior.

---

## **12. Summary: Best Practices**

1. **Choose the Right GC Collector:** 
   - Use G1GC for most applications by default.
   - ZGC/Shenandoah for low-latency needs.
   - Parallel GC for batch processing jobs.

2. **Monitor and Analyze:** 
   - Analyze GC logs regularly to detect bottlenecks.
   - Adjust heap size based on application workload and memory usage patterns.

3. **Avoid Full GC:** 
   - Tune heap size and object lifetimes to avoid triggering frequent Full GCs.
   - Use G1GC or other region-based collectors to reduce compaction overhead.

4. **Pre-tuning Advice:** 
   - Avoid over-tuning; start with default GC settings and only tweak after analyzing performance issues.

---