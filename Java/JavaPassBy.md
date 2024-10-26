# Is Java Pass By Reference or By Value ?

Java is stricly pass by value but lets go in depth.

### 1. **Reference Types vs. Primitive Types**

- **Primitive Types**: These are the basic data types in Java (e.g., `int`, `char`, `boolean`). When you pass a primitive type to a method, a **copy** of the value is made.
  
- **Reference Types**: These include objects, arrays, and instances of classes. When you pass a reference type to a method, you pass a reference (or pointer) to the actual object in memory, not the object itself.

### 2. **Pass by Value vs. Pass by Reference**

Java uses a mechanism called **pass by value** for method arguments, but it’s important to clarify how this applies to primitive and reference types.

#### a. **Pass by Value for Primitive Types**

When you pass a primitive type to a method, the method receives a copy of the variable's value. Any changes made to this copy do not affect the original variable.

**Example**:
```java
public class PassByValueExample {
    public static void main(String[] args) {
        int num = 10;
        modifyValue(num); // Passing primitive
        System.out.println(num); // Output: 10
    }

    public static void modifyValue(int value) {
        value = 20; // Only modifies the copy
    }
}
```

#### b. **Pass by Value for Reference Types**

When you pass a reference type to a method, the reference itself is passed by value. This means the method receives a copy of the reference to the object. While you can change the object's properties, you cannot change the reference to point to a different object.

**Example**:
```java
class MyClass {
    int value;

    MyClass(int value) {
        this.value = value;
    }
}

public class PassByReferenceExample {
    public static void main(String[] args) {
        MyClass obj = new MyClass(10);
        modifyObject(obj); // Passing reference
        System.out.println(obj.value); // Output: 20
    }

    public static void modifyObject(MyClass myObject) {
        myObject.value = 20; // Modifies the object's property
        // myObject = new MyClass(30); // This would not affect the original object reference only changes local myObject.
    }
}
```

### 3. **Why? Method Calls and Object References**

- **Method Calls**: When a method is called, a new stack frame is created, and local variables (including method parameters) are stored in this stack frame.

- **Object References**: Objects are stored in the heap, and the reference to these objects is passed to methods. When you modify the object’s state inside the method, it reflects outside the method because both the original reference and the parameter reference point to the same object in memory.

### 4. **Scope and Lifetime**

- **Scope**: Variables defined inside a method (local variables) can only be accessed within that method. Once the method completes, the local variables are destroyed.

- **Lifetime**: Objects in the heap remain as long as they are referenced. When no references exist (e.g., after the method execution, and no references are held), they become eligible for garbage collection.

### Summary

- Java is **pass-by-value**: 
  - Primitive types pass a copy of the value.
  - Reference types pass a copy of the reference to the object.
  
- Changes to the object through the reference affect the original object, but reassignment of the reference does not affect the original reference.