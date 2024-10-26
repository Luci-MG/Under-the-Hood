# Primitive Types and Reference Types.

### **Primitive Types in Java**  
Java has **8 primitive data types** that store simple values directly in memory.

| **Type**   | **Size**       | **Default Value** | **Range**                                | **Example**             |
|------------|----------------|-------------------|------------------------------------------|-------------------------|
| `byte`     | 1 byte (8 bits) | 0                 | -128 to 127                              | `byte b = 100;`         |
| `short`    | 2 bytes (16 bits) | 0               | -32,768 to 32,767                        | `short s = 30000;`      |
| `int`      | 4 bytes (32 bits) | 0               | -2^31 to (2^31)-1                        | `int i = 100000;`       |
| `long`     | 8 bytes (64 bits) | 0L              | -2^63 to (2^63)-1                        | `long l = 100000L;`     |
| `float`    | 4 bytes (32 bits) | 0.0f            | ~±3.4E38 (7 decimal digits precision)    | `float f = 3.14f;`      |
| `double`   | 8 bytes (64 bits) | 0.0             | ~±1.8E308 (15 decimal digits precision) | `double d = 3.14159;`   |
| `char`     | 2 bytes (16 bits) | '\u0000'        | Unicode characters (0 to 65,535)         | `char c = 'A';`         |
| `boolean`  | 1 bit (virtual)   | false           | true or false                           | `boolean b = true;`     |

---

### **Reference Types in Java**  
Reference types store **references (addresses)** to objects in memory, unlike primitive types that store values directly.

1. **`String`**:  Represents a sequence of characters.
   ```java
   String str = "Hello, World!";
   ```

2. **Arrays**:  Collections of elements of the same type.
   ```java
   int[] numbers = {1, 2, 3};
   ```

3. **Classes and Objects**:  Custom data types representing real-world entities.
   ```java
   class Person {
       String name;
   }
   Person p = new Person();
   ```

4. **Interfaces**:  Contracts that classes can implement.
   ```java
   interface Animal {
       void sound();
   }
   ```

5. **Enums**:  Special classes that define a set of constants.
   ```java
   enum Day {
       MONDAY, TUESDAY, WEDNESDAY;
   }
   ```

6. **Wrapper Classes**:  Used to convert primitive types into objects (auto-boxing/unboxing).
   | **Primitive** | **Wrapper Class** |
   |---------------|-------------------|
   | `byte`        | `Byte`            |
   | `short`       | `Short`           |
   | `int`         | `Integer`         |
   | `long`        | `Long`            |
   | `float`       | `Float`           |
   | `double`      | `Double`          |
   | `char`        | `Character`       |
   | `boolean`     | `Boolean`         |

---

### **Difference Between Primitive and Reference Types**  

| **Aspect**           | **Primitive Types**                         | **Reference Types**                            |
|----------------------|----------------------------------------------|-----------------------------------------------|
| **Storage**          | Store actual values.                        | Store references to objects in memory.       |
| **Memory Allocation**| Stored in stack memory.                     | Stored in heap memory.                       |
| **Default Values**   | Zero/false equivalents.                     | `null` for uninitialized references.         |
| **Examples**         | `int`, `char`, `boolean`.                   | `String`, Arrays, Classes, Interfaces, etc.  |