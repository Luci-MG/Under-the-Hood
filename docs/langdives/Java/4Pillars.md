# **4 Pillars - What, How, and Why ?**

## **Encapsulation**
**What**: Hiding the internal details of an object and only exposing necessary parts through public methods.

**Why**: It helps in **data hiding** and ensures controlled access to variables.

**How**: Use **private** variables to restrict direct access and provide **getters** and **setters** to access and modify the data.

???+ example
    ```java
    public class Person {
        private String name; // Encapsulated field

        // Getter
        public String getName() {
            return name;
        }

        // Setter
        public void setName(String name) {
            this.name = name;
        }
    }
    ```

## **Inheritance**
**What**: Allows a class (child/subclass) to acquire the properties and behaviors of another class (parent/superclass).

**How**: Use the **`extends`** keyword.

**Why**: Promotes **code reusability** and establishes a **parent-child relationship**.

???+ example
    ```java
    class Animal {
        public void sound() {
            System.out.println("Animals make sound");
        }
    }

    class Dog extends Animal {
        @Override
        public void sound() {
            System.out.println("Dog barks");
        }
    }
    ```

## **Polymorphism**

**What**: Ability to process objects differently based on their data type or class.

  - **Compile-time polymorphism**: Method overloading (same method name, different parameters).
  - **Runtime polymorphism**: Method overriding (subclass redefines a method of the superclass).

**Why**: Increases **flexibility** and supports **dynamic method invocation**.

???+ example
    ```java title="Method Overloading"
    class Calculator {
        public int add(int a, int b) {
            return a + b;
        }

        public double add(double a, double b) {
            return a + b;
        }
    }
    ```

    ```java title="Method Overriding"
    class Animal {
        public void sound() {
            System.out.println("Animal makes sound");
        }
    }

    class Cat extends Animal {
        @Override
        public void sound() {
            System.out.println("Cat meows");
        }
    }
    ```

## **Abstraction**
**What**: Hiding the complex implementation details and only exposing the essential features.

**Why**: Helps in achieving **modularity** and **loose coupling** between components.

**How**: Use **abstract classes** and **interfaces**.

???+ example
    ```java title="Abstract Class Example"
    abstract class Vehicle {
        abstract void start();
    }

    class Car extends Vehicle {
        @Override
        void start() {
            System.out.println("Car starts with a key");
        }
    }
    ```

    ```java title="Interface Example"
    interface Animal {
        void eat();
    }

    class Dog implements Animal {
        @Override
        public void eat() {
            System.out.println("Dog eats bones");
        }
    }
    ```

## **Summary**


| **Aspect**               | **Encapsulation**                                                        | **Inheritance**                                       | **Polymorphism**                                        | **Abstraction**                                                     |
|---------------------------|--------------------------------------------------------------------------|------------------------------------------------------|-------------------------------------------------------|----------------------------------------------------------------------|
| **Definition**            | Bundling data and methods together and restricting access to data.      | Mechanism for a subclass to acquire properties of a parent class. | Allowing methods to take different forms (overloading/overriding). | Hiding implementation details while showing only essential features.|
| **Focus**                 | Protecting data and providing controlled access.                        | Code reuse and establishing a parent-child hierarchy. | Dynamic behavior based on object type.               | Simplifying complex systems by exposing only key details.           |
| **Achieved Through**      | Using private fields, and public getters/setters.                       | Using the `extends` keyword to derive subclasses.     | Overloading (compile-time) and overriding (runtime).  | Using `abstract` classes or `interfaces`.                          |
| **Key Benefit**           | Data hiding and modular code.                                           | Reduces redundancy and promotes code reuse.          | Flexibility and extensibility of behavior.            | Promotes loose coupling and modularity.                            |
| **Access Modifiers**      | Requires `private`, `protected`, or `public`.                          | Involves all inheritance-accessible modifiers.        | Leverages method visibility across class hierarchies. | Abstract methods can be `protected` or `public` (not private).      |
| **Real-World Analogy**    | A capsule with medicine inside it hides the internal components.        | A child inheriting traits from their parents.         | A shape object behaving differently as circle/square. | A remote control exposing buttons without showing internal circuits.|
| **Code Dependency**       | Independent within the class.                                           | Dependent on parent-child relationship.              | Involves multiple forms of a single method/class.     | Can work with unrelated classes sharing common behavior.           |

---