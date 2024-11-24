# **Access modifiers**

## **Public**  
**Keyword**: `public`  
**Access**: Accessible from **anywhere** (inside/outside the class, package, or project).  
**Usage**: Typically used for **classes, methods, and variables** that need global access.  
???+ example
    ```java
    public class MyClass {
        public int value = 10;
        public void display() {
            System.out.println("Public method");
        }
    }
    ```

## **Private**  
**Keyword**: `private`  
**Access**: Accessible **only within the same class**.  
**Usage**: Used to **hide class fields or methods**, following the principle of **encapsulation**.  
???+ example
    ```java
    public class MyClass {
        private int value = 10; // Not accessible outside this class

        private void display() {
            System.out.println("Private method");
        }
    }
    ```

## **Protected**  
**Keyword**: `protected`  
**Access**: Accessible **within the same package** and **by subclasses (even if outside the package)**.  
**Usage**: Useful when extending classes across packages.  
???+ example
    ```java
    public class MyClass {
        protected int value = 10;

        protected void display() {
            System.out.println("Protected method");
        }
    }
    ```
!!! note
    If accessed by a subclass in a **different package**, it must be through inheritance (not directly via an instance).


## **Package-Private**  
**Keyword**: *No keyword*  (Default Access)  
**Access**: Accessible only **within the same package**.  
**Usage**: Used for classes and members that **don’t need to be accessed outside their package**.  
???+ example
    ```java
    class MyClass { // No access modifier, so it's package-private
        int value = 10;

        void display() {
            System.out.println("Package-private method");
        }
    }
    ```
!!! note 
    Package-private is the default if no modifier is specified.


## **Access Summary** 

| Modifier       | Same Class | Same Package | Subclass (Different Package) | Other Packages |
|----------------|------------|--------------|------------------------------|----------------|
| `public`       | ✅         | ✅           | ✅                           | ✅             |
| `protected`    | ✅         | ✅           | ✅ (via inheritance)         | ❌             |
| (default)      | ✅         | ✅           | ❌                           | ❌             |
| `private`      | ✅         | ❌           | ❌                           | ❌             |

---