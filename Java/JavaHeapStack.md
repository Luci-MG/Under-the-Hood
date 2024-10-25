# Java Memory Model

Java uses a memory model that divides memory into different areas, primarily the **heap** and **stack**. 

### 1. **Heap Memory**

- **Purpose**: The heap is used for dynamic memory allocation. Objects created using the `new` keyword are stored in the heap.
  
- **Lifetime**: Objects in the heap remain in memory until they are no longer referenced and are garbage collected. This means the lifetime of an object is not tied to the scope of a method.

- **Size**: The heap size can be adjusted using JVM options (e.g., `-Xms` for initial heap size and `-Xmx` for maximum heap size). 

- **Access**: Accessing memory in the heap is slower than in the stack due to its dynamic nature and the potential for fragmentation.

- **Garbage Collection**: Java automatically manages memory in the heap through a process called garbage collection, which frees up memory by removing objects that are no longer in use.

- **When**: Heap memory is allocated at execution time when objects are created using the `new` keyword.
- **How**: When you instantiate an object, memory for that object is dynamically allocated from the heap. For example:
  ```java
  MyClass obj = new MyClass(); // Heap allocation
  ```

- **Use Cases**: 
  - Storing objects (e.g., instances of classes).
  - Arrays (e.g., `int[] arr = new int[10];`).

### 2. **Stack Memory**

- **Purpose**: The stack is used for static memory allocation. It stores method call frames, which contain local variables, method parameters, and return addresses.

- **Lifetime**: The lifetime of a variable in the stack is limited to the duration of the method call. Once the method returns, the stack frame is popped off, and the memory is reclaimed.

- **Size**: Stack memory is generally smaller than heap memory. The stack size can also be adjusted using JVM options (e.g., `-Xss`).

- **Access**: Accessing stack memory is faster than heap memory because it follows a Last In, First Out (LIFO) order, allowing for quick allocation and deallocation.

- **When**: Stack memory is allocated at execution time, specifically when a method is called.
- **How**: Each time a method is invoked, a new stack frame is created. This frame contains:
  - Local variables
  - Method parameters
  - Return address

- **Use Cases**: 
  - Local variables (e.g., `int num = 5;` inside a method).
  - Method call parameters.

### Key Differences

| Feature                  | Heap                             | Stack                            |
|--------------------------|----------------------------------|----------------------------------|
| **Allocation**           | Dynamic                          | Static                           |
| **Lifetime**             | Until garbage collected          | Duration of method call          |
| **Memory Size**          | Larger (configurable)            | Smaller (configurable)           |
| **Access Speed**         | Slower                           | Faster                           |
| **Data Type**            | Objects, arrays                  | Primitive types, references      |
| **Management**           | Garbage collection                | Automatically managed by JVM     |

### Example

```java
public class MemoryExample {
    public static void main(String[] args) {
        int localVar = 10; // Stack memory

        MemoryExample obj = new MemoryExample(); // Heap memory
        obj.display(localVar); // Passing parameter, stack memory
    }

    public void display(int param) { // Stack memory
        System.out.println(param);
        String message = "Hello"; // Heap memory (String object)
    }
}
```
---