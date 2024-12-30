# **Java Memory Model**

Java uses a memory model that divides memory into different areas, primarily the heap and stack. 

## **Heap Memory**

The heap is mainly used for dynamic memory allocation. Objects created using the `new` keyword are stored in the heap. Coming to it's life time objects in the heap remain in memory until they are no longer referenced and are garbage collected. This means the lifetime of an object is not tied to the scope of a method and Accessing memory in the heap is slower than in the stack due to its dynamic nature and the potential for fragmentation.

!!! note
    - The heap size can be adjusted using JVM options (e.g., `-Xms` for initial heap size and `-Xmx` for maximum heap size). 
    - Java automatically manages memory in the heap through a process called garbage collection, which frees up memory by removing objects that are no longer in use.
    - Heap memory is allocated at execution time when objects are created using the `new` keyword.
    - When you instantiate an object, memory for that object is dynamically allocated from the heap.
      ```java title="Example"
      MyClass obj = new MyClass(); // Heap allocation
      ```

## **Stack Memory**

The stack is mainly used for static memory allocation. It stores method call frames, which contain local variables, method parameters, and return addresses. coming to the lifetime of a variable in the stack is limited to the duration of the method call. Once the method returns, the stack frame is popped off, and the memory is reclaimed and accessing stack memory is faster than heap memory because it follows a Last In, First Out (LIFO) order, allowing for quick allocation and deallocation.

!!! note
    - Stack memory is generally smaller than heap memory. The stack size can also be adjusted using JVM options (e.g., `-Xss`).
    - Stack memory is allocated at execution time, specifically when a method is called.
    - Each time a method is invoked, a new stack frame is created. This frame contains: Local variables, Method parameters, Return address


## **Example**
!!! example 
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

## **Differences**

| Feature                  | Heap                             | Stack                            |
|--------------------------|----------------------------------|----------------------------------|
| **Allocation**           | Dynamic                          | Static                           |
| **Lifetime**             | Until garbage collected          | Duration of method call          |
| **Memory Size**          | Larger (configurable)            | Smaller (configurable)           |
| **Access Speed**         | Slower                           | Faster                           |
| **Data Type**            | Objects, arrays                  | Primitive types, references      |
| **Management**           | Garbage collection                | Automatically managed by JVM     |

---