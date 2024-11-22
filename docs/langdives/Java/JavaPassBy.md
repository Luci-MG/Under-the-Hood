# **Is Java Pass By Value or By Reference** ?

Java is stricly pass by value but lets go in depth.

## **Reference vs Primitive**

**Primitive Types**: These are the basic data types in Java (e.g., `int`, `char`, `boolean`). When you pass a primitive type to a method, a **copy** of the value is made.
  
**Reference Types**: These include objects, arrays, and instances of classes. When you pass a reference type to a method, you pass a reference (or pointer) to the actual object in memory, not the object itself.

## **Pass by Value**

Java uses a mechanism called **pass by value** for method arguments, but it’s important to clarify how this applies to primitive and reference types.

### **Primitive Types**

When you pass a primitive type to a method, the method receives a copy of the variable's value. Any changes made to this copy do not affect the original variable.

???+ example
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

### **Reference Types**

When you pass a reference type to a method, the reference itself is passed by value. This means the method receives a copy of the reference to the object. While you can change the object's properties, you cannot change the reference to point to a different object.

???+ example
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

## **Why?**

 When a method is called, a new stack frame is created, and local variables (including method parameters) are stored in this stack frame. Objects are stored in the heap, and the reference to these objects is passed to methods. When you modify the object’s state inside the method, it reflects outside the method because both the original reference and the parameter reference point to the same object in memory.

## **Scope and Lifetime**

- **Scope**: Variables defined inside a method (local variables) can only be accessed within that method. Once the method completes, the local variables are destroyed.

- **Lifetime**: Objects in the heap remain as long as they are referenced. When no references exist (e.g., after the method execution, and no references are held), they become eligible for garbage collection.

## **Summary**

Java is **pass-by-value**: 

- Primitive types pass a copy of the value.
- Reference types pass a copy of the reference to the object.

Changes to the object through the reference affect the original object, but reassignment of the reference does not affect the original reference.