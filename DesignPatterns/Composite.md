# **Composite Design Pattern**

The **Composite Pattern** is a structural design pattern used in software design to represent part-whole hierarchies. It enables you to build complex object structures by treating both individual objects and compositions of objects uniformly.

---

### 1. **What is the Composite Pattern?**
The Composite pattern allows you to treat a group of objects in the same way as a single object. This is especially useful when building **tree structures** (like directories or UI components). 

The idea is to define a **common interface** for all the objects, whether simple or complex, so they can be treated uniformly.

- **Component**: A base interface for both leaf objects and composite objects.
- **Leaf**: An individual object that doesn't contain other objects.
- **Composite**: A container of other `Component` objects. It may contain both Leafs and other Composites.

---

### 2. **When to Use the Composite Pattern**
1. **You have a tree structure.**
   - Example: Filesystem, organizational hierarchies, UI components (panels, buttons, etc.).
2. **You need to treat individual and composite objects uniformly.**
   - Example: A method like `draw()` should work for both individual shapes and a group of shapes.
3. **You want to avoid if-else checks to determine if an object is an individual or a container.**
4. **You want objects to dynamically add children.**

---

### 3. **How to Create a Composite Pattern?**

#### **Basic Structure in Java**

Below is the UML breakdown:

```
Component (Interface or Abstract class)
   ├── Leaf (Concrete class)
   └── Composite (Concrete class containing Components)
```

#### **Java Example**

```java
// 1. Component Interface
interface Component {
    void showDetails();  // Common operation for both Leaf and Composite
}

// 2. Leaf Class (Single object)
class Employee implements Component {
    private String name;
    private String position;

    public Employee(String name, String position) {
        this.name = name;
        this.position = position;
    }

    @Override
    public void showDetails() {
        System.out.println(name + " works as " + position);
    }
}

// 3. Composite Class (Composite of Components)
class Department implements Component {
    private List<Component> employees = new ArrayList<>();

    public void addEmployee(Component employee) {
        employees.add(employee);
    }

    public void removeEmployee(Component employee) {
        employees.remove(employee);
    }

    @Override
    public void showDetails() {
        for (Component employee : employees) {
            employee.showDetails();
        }
    }
}

// 4. Client Code
public class CompositePatternDemo {
    public static void main(String[] args) {
        // Create individual employees
        Component emp1 = new Employee("John", "Developer");
        Component emp2 = new Employee("Doe", "Tester");

        // Create a department and add employees to it
        Department department = new Department();
        department.addEmployee(emp1);
        department.addEmployee(emp2);

        // Show details
        System.out.println("Department Details:");
        department.showDetails();
    }
}
```

**Output:**
```
Department Details:
John works as Developer
Doe works as Tester
```

---

### 4. **Where Not to Use Composite Pattern**

- If your object structure is **flat** without any hierarchy.
- If objects have no relation to one another, there’s no need for such complexity.
- If performance is critical and deep hierarchies could result in expensive recursive calls.

---

### 5. **Ways to Create a Composite Pattern**

1. **With Interfaces or Abstract Classes** (as shown above).
2. **With Generics**, allowing better type-safety.
3. **With Builders**: Use a Builder pattern to assemble the composite structure dynamically.
4. **Immutable Composites**: Create composite objects where elements are added only through constructor injection to prevent accidental changes.

---

### 6. **Integrating Composite Pattern with Spring Boot**

In Spring Boot, the Composite pattern can fit into cases where you model **tree-based structures** in your business logic, such as:

1. **Entity Relationships in JPA**: Modeling nested categories, departments, or menus.
2. **Business Service Layer**: Creating a unified API to handle both individual and composite objects.

### **Example: Product Category Service with Composite Pattern in Spring Boot**

```java
// 1. Component Interface
public interface ProductCategory {
    String getName();
    void showCategoryDetails();
}

// 2. Leaf Class
public class Product implements ProductCategory {
    private String name;

    public Product(String name) {
        this.name = name;
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void showCategoryDetails() {
        System.out.println("Product: " + name);
    }
}

// 3. Composite Class
public class Category implements ProductCategory {
    private String name;
    private List<ProductCategory> children = new ArrayList<>();

    public Category(String name) {
        this.name = name;
    }

    public void add(ProductCategory category) {
        children.add(category);
    }

    public void remove(ProductCategory category) {
        children.remove(category);
    }

    @Override
    public String getName() {
        return name;
    }

    @Override
    public void showCategoryDetails() {
        System.out.println("Category: " + name);
        for (ProductCategory child : children) {
            child.showCategoryDetails();
        }
    }
}

// 4. Controller in Spring Boot
@RestController
@RequestMapping("/categories")
public class CategoryController {

    @GetMapping("/example")
    public void example() {
        // Creating products
        ProductCategory p1 = new Product("Laptop");
        ProductCategory p2 = new Product("Phone");

        // Creating a category and adding products
        Category electronics = new Category("Electronics");
        electronics.add(p1);
        electronics.add(p2);

        // Display details
        electronics.showCategoryDetails();
    }
}
```

**Sample Output when calling `/categories/example`:**

```
Category: Electronics
Product: Laptop
Product: Phone
```

#### **Spring Boot Considerations**

- **Use in Controllers**: You can use the composite pattern to model responses from REST endpoints.
- **Use in Services**: Combine multiple services (leaf and composite) in a unified API.
- **Use with JPA Entities**: If your entities are hierarchical, you can apply the Composite pattern with relationships (e.g., `@OneToMany`).

---

### 7. **Why Use Composite Pattern?**

1. **Tree Structure Representation**: Perfect for hierarchical data like organizations, UI components, or filesystems.
2. **Unified API**: Makes it easy to handle both individual and composite objects using the same operations.
3. **Extensibility**: You can add new types of components without modifying existing code (Open-Closed Principle).
4. **Reduces Conditional Logic**: Avoids if-else statements by treating all objects uniformly.

---

### 8. **How to Use Composite Pattern Effectively?**

- **Design around hierarchies:** Use it when your model naturally fits a tree structure.
- **Leverage abstraction:** Ensure you design a robust `Component` interface for both Leafs and Composites.
- **Avoid unnecessary depth:** Keep your object hierarchy shallow to prevent performance issues with recursive calls.

---

### 9. **Summary**

The Composite Pattern is a powerful structural pattern for managing **hierarchical, tree-like structures**. It allows **uniform handling** of individual and composite objects, making it ideal for UI elements, filesystems, or business domains with nested elements. When integrating with **Spring Boot**, it works well in **controllers, services**, or **JPA entities** for modeling hierarchical data.

However, avoid using it when there’s no hierarchy or when performance is critical (deep recursion). Use it wisely, and it can help you **reduce complexity** and **simplify your code**.

---