# Reference Types In Depth.

how **memory management, object references, and behaviors** work in Java, with a focus on **String handling** and other reference types like **arrays, classes, and wrapper objects**.

---

## **In-Depth Explanation of Reference Types in Java**

### **How Reference Types Work Internally**  
- **Primitive types** store values directly in **stack memory**.
- **Reference types** store **references (addresses)** to objects located in **heap memory**.
- When you assign a reference type (e.g., `String` or an array), **only the reference (address)** is copied, not the actual data. This means **multiple references can point to the same object**.

---

### **1. String in Java – Immutability and String Pool**  
The **`String`** class in Java is a special reference type with some unique behaviors.

#### **String Immutability**  
- Strings are **immutable**: once a `String` object is created, it cannot be changed.
- Any modification on a `String` results in the creation of a **new object** in memory.


#### **Where Strings are Stored?**
1. **String Pool (Interned Strings)**:  
   - A **special memory area inside the heap** used to store string literals.
   - If a string literal like `"Hello"` is created, Java first checks the **string pool** to see if it already exists. If it does, it returns the reference from the pool. If not, the string is added to the pool.

2. **Heap Memory**:  
   - When you use the **`new` keyword**, a new `String` object is always created in the **heap memory**.
   - Even if the same string already exists in the **string pool**, the `new` keyword forces the creation of a **separate instance** in the heap.

---
  
**Example:**
```java
String s1 = "Hello";  // Stored in the String Pool
String s2 = "Hello";  // s2 points to the same object as s1

System.out.println(s1 == s2);  // true (same reference)
```

#### **Using `new` Keyword with Strings**  
- When you use `new String()`, Java **forces the creation of a new object** in heap even if the same string exists in the pool.

**Example:**
```java
String s1 = new String("Hello");
String s2 = "Hello";

System.out.println(s1 == s2);  // false (different references)
```

**Summary**:  
- `"Hello"` uses **String Pool**.
- `new String("Hello")` creates a **new object** outside the pool in the heap.

---

### **2. Arrays – Reference Handling**  
Arrays are reference types, meaning **the array variable stores a reference** to the memory location where the array data is stored.

**Example:**
```java
int[] arr1 = {1, 2, 3};
int[] arr2 = arr1;  // arr2 now references the same array as arr1

arr2[0] = 10;  // Modifies the original array

System.out.println(arr1[0]);  // Output: 10 (both arr1 and arr2 reference the same array)
```

#### **How Array References Work:**
- `arr1` and `arr2` point to the **same memory location** in the heap.
- If you change the array via `arr2`, the change will reflect in `arr1` because both refer to the **same object**.

---

### **3. Classes and Objects – Reference Behavior**  
When you create an object using `new`, the **reference variable** points to the object in heap memory.

**Example:**
```java
class Person {
    String name;
}

Person p1 = new Person();
p1.name = "Alice";

Person p2 = p1;  // p2 points to the same object as p1
p2.name = "Bob";

System.out.println(p1.name);  // Output: Bob (both references point to the same object)
```

#### **How References Work with Objects:**
- `p1` and `p2` point to the **same Person object**. Changing the object via `p2` affects `p1`.
- This is how **shallow copies** work — only the reference is copied, not the object itself.

---

### **4. Wrapper Classes – Autoboxing and Reference Behavior**  
Wrapper classes (`Integer`, `Double`, `Boolean`, etc.) wrap primitive types into objects. These are reference types, and Java performs **autoboxing/unboxing** to convert between primitive types and wrapper objects.

**Example:**
```java
Integer num1 = 100;
Integer num2 = 100;

System.out.println(num1 == num2);  // true (for values within -128 to 127)

Integer num3 = 200;
Integer num4 = 200;

System.out.println(num3 == num4);  // false (new objects for values beyond 127)
```

#### **Wrapper Caching**  
- Java caches `Integer` objects in the **range -128 to 127** for performance.
- Beyond this range, **new objects** are created.

---

### **5. Reference Copy vs. Deep Copy**

- **Shallow Copy**: Copies only the **reference**, so both variables refer to the same object.
- **Deep Copy**: Creates a **new object** with the same data.

**Example of Shallow Copy:**
```java
int[] original = {1, 2, 3};
int[] shallowCopy = original;  // Points to the same array

shallowCopy[0] = 100;
System.out.println(original[0]);  // Output: 100
```

**Example of Deep Copy:**
```java
int[] original = {1, 2, 3};
int[] deepCopy = original.clone();  // Creates a new array

deepCopy[0] = 100;
System.out.println(original[0]);  // Output: 1
```

---

### **Null Reference and `NullPointerException`**  
- When a reference is **not initialized**, it holds the value **`null`**.
- Accessing a field or method on a **`null` reference** throws a **`NullPointerException`**.

**Example:**
```java
Person p = null;
System.out.println(p.name);  // Throws NullPointerException
```

---

### **Garbage Collection and References**  
- Java uses **Garbage Collection** to manage memory.
- When no references point to an object, it becomes **eligible for garbage collection**.

```java
Person p1 = new Person();  // Object created
p1 = null;  // Now eligible for garbage collection
```

---

### **Key Takeaways**

1. **Strings**: Immutable, stored in the **String Pool** if created with literals. `new String()` creates a separate object.
2. **Arrays**: Reference types, so multiple variables can point to the same array object.
3. **Classes**: Objects are referenced in memory; multiple references can point to the same object.
4. **Wrapper Classes**: Use caching for certain ranges (e.g., `Integer` values from -128 to 127).
5. **Garbage Collection**: Objects are eligible for garbage collection when no active references point to them.

---

The **String Pool** (also called the **intern pool**) in Java is implemented using a **Hash Table-like data structure** internally. Let’s explore the design and behavior behind this structure:

---

## **How String Pool is Implemented in Java**

1. **Hash Table Concept:**  
   - The **String Pool** uses a **Hash Map-like structure** internally, where the **key** is the string literal, and the **value** is a reference to the interned string in the pool.
   - This ensures **fast lookups** and **deduplication** of identical strings because hash-based structures allow **O(1) average-time complexity** for lookup operations.

2. **Behavior of String Pool:**
   - If a new string literal is created, the pool:
     - **Checks** if the string already exists (by computing its hash).
     - If the string **exists**, it returns the **existing reference**.
     - If not, the string is **added to the pool**.

3. **Rehashing and Thread Safety:**  
   - The interned pool is **part of the JVM's heap** and managed by the **Java String class**.
   - **Since Java 7**, the pool is **stored in the heap** instead of the **PermGen** space to allow better memory management.
   - The pool structure is **thread-safe**, meaning multiple threads can safely use the pool.

---

### **Internally: Hash-Based Structure Pseudocode**

Here’s a simplified conceptual pseudocode to show how the pool works internally:

```java
class StringPool {
    private static Map<String, String> pool = new HashMap<>();

    public static String intern(String str) {
        if (pool.containsKey(str)) {
            return pool.get(str);  // Return existing reference
        } else {
            pool.put(str, str);    // Add to the pool
            return str;
        }
    }
}
```

- When calling `String.intern()`, Java **interns the string**, meaning it adds the string to the pool if it's not already present.

---

### **String Pool Usage Example with `intern()`**

```java
public class Main {
    public static void main(String[] args) {
        String s1 = new String("Hello");
        String s2 = s1.intern();  // Adds "Hello" to the pool, if not already present

        String s3 = "Hello";  // Uses the interned string from the pool

        System.out.println(s2 == s3);  // true (same reference from the pool)
    }
}
```

---

### **Why Use Hash Table for the Pool?**

- **Fast Lookups:** A hash table allows **constant time complexity (O(1))** for lookups.
- **Efficient Memory Management:** Strings are immutable, so duplicate strings are avoided, improving memory usage.
- **Thread Safety:** The JVM ensures that the pool is **safe for concurrent access**, so multiple threads can use the intern pool efficiently.

---

### **Key Takeaways:**

1. **Since Java 7**, the string pool has been moved to the **heap**, so it grows dynamically with the application’s memory needs.
2. **Before Java 7**, the pool was in **PermGen** space, which had a fixed size and could lead to `OutOfMemoryError` if too many strings were interned.
3. **Interning Costs:** Calling `intern()` on every string can **increase the overhead** slightly (because of the hash lookup). It’s only beneficial for **reducing memory usage** when you expect many repeated strings.

---

### **Summary**

- The **String Pool** is implemented using a **Hash Table-like data structure**.
- This allows for **efficient string reuse** through fast lookups and ensures **no duplicate literals** are created.
- Strings added via literals or `intern()` are stored in the pool, with **existing references returned** on subsequent requests.
