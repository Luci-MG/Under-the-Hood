# Java 4 Pillars - What, How, and Why ?

### 1. **Encapsulation**
- **What**: Hiding the internal details of an object and only exposing necessary parts through public methods.
- **How**:
  - Use **private** variables to restrict direct access.
  - Provide **getters** and **setters** to access and modify the data.

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

- **Why**: It helps in **data hiding** and ensures controlled access to variables.

---

### 2. **Inheritance**
- **What**: Allows a class (child/subclass) to acquire the properties and behaviors of another class (parent/superclass).
- **How**:
  - Use the **`extends`** keyword.

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

- **Why**: Promotes **code reusability** and establishes a **parent-child relationship**.

---

### 3. **Polymorphism**
- **What**: Ability to process objects differently based on their data type or class.
  - **Compile-time polymorphism**: Method overloading (same method name, different parameters).
  - **Runtime polymorphism**: Method overriding (subclass redefines a method of the superclass).

**Method Overloading**:
```java
class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }
}
```

**Method Overriding**:
```java
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

- **Why**: Increases **flexibility** and supports **dynamic method invocation**.

---

### 4. **Abstraction**
- **What**: Hiding the complex implementation details and only exposing the essential features.
- **How**:
  - Use **abstract classes** and **interfaces**.

**Abstract Class Example**:
```java
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

**Interface Example**:
```java
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

- **Why**: Helps in achieving **modularity** and **loose coupling** between components.

---