# **Prototype Pattern**

The **Prototype Pattern** is a **creational design pattern** used when the cost of creating a new object is expensive or complicated. Instead of creating new instances from scratch, this pattern suggests **cloning existing objects** to produce new ones.

---

### 1. **What is the Prototype Pattern?**

The **Prototype Pattern** allows cloning or copying existing instances to create new ones, ensuring that new objects are created **without going through the expensive or complex instantiation process** repeatedly.

The main idea is:
- Objects are created by copying or **cloning an existing prototype** object.
- Each prototype maintains its state, and the cloned objects are **independent copies**.
- Useful when the creation of an object is **resource-intensive** (e.g., large data loads, configurations, etc.).

---

### 2. **How to Create a Prototype?**

In Java, the **Prototype Pattern** is implemented by making the class implement the **`Cloneable`** interface and **overriding the `clone()`** method from the `Object` class.

**Key Methods:**
- `clone()` method is used to create an identical copy of an object.
- **Deep Clone vs. Shallow Clone**:
  - **Shallow Clone**: Only the reference of objects is copied (not the actual object).
  - **Deep Clone**: All nested objects are also cloned to ensure full independence.

---

### 3. **When to Use / Why to Use?**

You should use the **Prototype Pattern**:
- **When creating an object is expensive**: e.g., reading a database, complex object initialization, or network-intensive setup.
- **When you want to avoid creating new instances** and prefer copying existing objects.
- **When your code requires many objects of a similar type**, where only slight modifications are made to the original object.
- **When the class structure or object creation is complex** and involves multiple configurations (e.g., with builders, factories, etc.).

---

### 4. **Where NOT to Use It?**

The **Prototype Pattern** is not recommended:
- When **creating a new instance is inexpensive** and straightforward.
- When **object state changes frequently**, as the cloned object may quickly become stale or out-of-sync with the original.
- **If immutable objects** are required, as the whole point of this pattern is to create modifiable, independent copies.

---

### 5. **Ways to Implement the Prototype Pattern in Java**

**Shallow Copy:**
- Only the references of the fields are copied, not the objects themselves.

**Deep Copy:**
- A full independent copy of all nested objects is created.

Here is an example of **Shallow and Deep Copy** using the **Prototype Pattern in Java**:

#### Example of Prototype in Java

```java
class Address {
    String street;
    String city;

    public Address(String street, String city) {
        this.street = street;
        this.city = city;
    }

    // Deep Copy
    public Address(Address address) {
        this.street = address.street;
        this.city = address.city;
    }

    @Override
    public String toString() {
        return street + ", " + city;
    }
}

class Employee implements Cloneable {
    String name;
    Address address;

    public Employee(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    // Shallow Copy
    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    // Deep Copy
    public Employee deepClone() {
        return new Employee(this.name, new Address(this.address));
    }

    @Override
    public String toString() {
        return "Employee: " + name + ", Address: " + address;
    }
}

public class PrototypeExample {
    public static void main(String[] args) throws CloneNotSupportedException {
        Employee emp1 = new Employee("Alice", new Address("123 Street", "New York"));
        
        // Shallow Clone
        Employee emp2 = (Employee) emp1.clone();
        
        // Deep Clone
        Employee emp3 = emp1.deepClone();

        System.out.println("Original: " + emp1);
        System.out.println("Shallow Copy: " + emp2);
        System.out.println("Deep Copy: " + emp3);

        // Modify the original object to see the effect on shallow vs deep copy
        emp1.address.street = "456 Avenue";
        
        System.out.println("After modifying the original object:");
        System.out.println("Original: " + emp1);
        System.out.println("Shallow Copy: " + emp2);
        System.out.println("Deep Copy: " + emp3);
    }
}
```

#### Output:
```
Original: Employee: Alice, Address: 123 Street, New York
Shallow Copy: Employee: Alice, Address: 123 Street, New York
Deep Copy: Employee: Alice, Address: 123 Street, New York

After modifying the original object:
Original: Employee: Alice, Address: 456 Avenue, New York
Shallow Copy: Employee: Alice, Address: 456 Avenue, New York
Deep Copy: Employee: Alice, Address: 123 Street, New York
```

In the above example:
- **Shallow Copy** (`clone()`): Modifying the original affects the copy (since both reference the same `Address` object).
- **Deep Copy** (`deepClone()`): The copy remains unaffected because it contains a completely new `Address` object.

---

### 6. **Integrating the Prototype Pattern with Spring Boot**

Spring Framework allows defining **prototype-scoped beans**. Each time you request a bean with the prototype scope, Spring returns a new instance, effectively following the **Prototype Pattern**.

#### How to Use Prototype Scope in Spring Boot

1. Add `@Scope` annotation to the bean definition.
2. Use **`prototype` scope** to ensure each request gets a new object.

Here’s an example in Spring Boot:

#### Example: Prototype Scope in Spring Boot

##### 1. Bean Definition:

```java
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class Employee {
    public Employee() {
        System.out.println("New Employee instance created.");
    }
}
```

##### 2. Controller:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class EmployeeController {

    @Autowired
    private Employee employee1;

    @Autowired
    private Employee employee2;

    @GetMapping("/employees")
    public String getEmployees() {
        return "Employee 1: " + employee1 + " | Employee 2: " + employee2;
    }
}
```

##### 3. Application Output:

When you run this application and hit the `/employees` endpoint, you will see:

```
New Employee instance created.
New Employee instance created.
```

This shows that a **new instance is created each time** a prototype-scoped bean is injected.

---

### 7. **When to Use / Where Not to Use Prototype in Spring Boot?**

- **Use**:
  - When you need **stateless beans** that are short-lived and used temporarily.
  - When objects need to be **created dynamically** (e.g., user session-based objects).

- **Avoid**:
  - For **singleton beans** or objects that must be shared across the application.
  - When object creation is cheap, and cloning provides no significant benefit.

---

### Summary

The **Prototype Pattern** offers an elegant way to clone existing objects, saving the overhead of complex object creation. It fits well when objects are **expensive to create** or **share the same initial configuration**. With **Java's cloning mechanisms** and **Spring Boot's prototype scope**, it is easy to implement. However, care must be taken when handling deep versus shallow copies, and the pattern should be avoided when objects are inexpensive to create.

---