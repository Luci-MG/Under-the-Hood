# **Builder Design**

## **What ?**

The Builder Pattern is a creational design pattern that allows the construction of complex objects step by step. It separates the construction process from the actual object, giving more control over the construction process.

This Pattern simplifies the creation of complex objects with many optional fields by enabling incremental construction through method chaining, avoiding constructors with numerous parameters. It's ideal for objects requiring various configurations or optional parameters.


## **When to Use ?**
- When the class has many fields (especially optional ones).
- When you want to avoid telescoping constructors (having constructors with increasing numbers of parameters).
- When an object must be immutable but needs complex construction logic.
- When you want to ensure a clear, readable way to build an object, rather than calling constructors directly.


## **Why Use ?**
- Improves Code Readability by avoiding large constructors by using chained methods.
- Enforces Immutability after construction, the built object can remain immutable.
- Increases Flexibility by supporting different configurations while keeping the same building logic.
- Clear Separation of Logic Like The construction logic stays in the builder, while the object remains a simple data container.

## **Where Not to Use ?**
- If the object is simple with only a few attributes, using a builder can overcomplicate things.
- When performance is critical—since creating the builder involves some extra overhead (though minimal).
- If immutability is not necessary, simpler setter methods might suffice.


## **Advantages**
- Clear and readable object construction.
- Immutable Objects once built, the object cannot be modified.
- Flexible Object Creation supports optional parameters without overloading constructors.
- Easier Maintenance with new fields can be easily added without modifying existing constructors.


## **How to Implement ?**

??? example "Simple Example"

    Below is a basic Java example demonstrating the pattern. Assume we need to build a `Car` object with several optional fields.

    ```java title="Simple Builder Implementation"
    public class Car {
        // Required fields
        private final String make;
        private final String model;

        // Optional fields
        private final String color;
        private final int year;
        private final boolean automatic;

        // Private constructor accessible only through Builder
        private Car(Builder builder) {
            this.make = builder.make;
            this.model = builder.model;
            this.color = builder.color;
            this.year = builder.year;
            this.automatic = builder.automatic;
        }

        // Getters (optional, based on your needs)
        public String getMake() { return make; }
        public String getModel() { return model; }
        public String getColor() { return color; }
        public int getYear() { return year; }
        public boolean isAutomatic() { return automatic; }

        // Static inner Builder class
        public static class Builder {
            // Required fields
            private final String make;
            private final String model;

            // Optional fields initialized to default values
            private String color = "White";
            private int year = 2020;
            private boolean automatic = true;

            // Builder constructor with required fields
            public Builder(String make, String model) {
                this.make = make;
                this.model = model;
            }

            // Setter-like methods for optional fields, returning the builder object
            public Builder color(String color) {
                this.color = color;
                return this;
            }

            public Builder year(int year) {
                this.year = year;
                return this;
            }

            public Builder automatic(boolean automatic) {
                this.automatic = automatic;
                return this;
            }

            // Build method to create the final Car object
            public Car build() {
                return new Car(this);
            }
        }

        @Override
        public String toString() {
            return "Car [make=" + make + ", model=" + model + 
                ", color=" + color + ", year=" + year + 
                ", automatic=" + automatic + "]";
        }
    }
    ```

    ```java title="Usage of the Builder Pattern"
    public class Main {
        public static void main(String[] args) {
            // Using the builder to create a Car object
            Car car = new Car.Builder("Tesla", "Model S")
                                .color("Red")
                                .year(2023)
                                .automatic(true)
                                .build();

            System.out.println(car);
        }
    }
    ```

    **Output:**
    ```
    Car [make=Tesla, model=Model S, color=Red, year=2023, automatic=true]
    ```

??? example "Spring Boot Example"

    In Spring Boot, you often need to build objects like DTOs, configurations, or entities with complex structures. Using the Builder Pattern can make object construction more manageable, especially when working with REST APIs.

    ```java title="Using Builder Pattern for DTOs in Spring Boot"
    // Let's assume a UserDTO object for API responses.
    public class UserDTO {
        private final String username;
        private final String email;
        private final String role;

        private UserDTO(Builder builder) {
            this.username = builder.username;
            this.email = builder.email;
            this.role = builder.role;
        }

        public static class Builder {
            private String username;
            private String email;
            private String role;

            public Builder username(String username) {
                this.username = username;
                return this;
            }

            public Builder email(String email) {
                this.email = email;
                return this;
            }

            public Builder role(String role) {
                this.role = role;
                return this;
            }

            public UserDTO build() {
                return new UserDTO(this);
            }
        }
    }
    ```

    ```java title="Controller Example with Builder Pattern in Spring Boot"
    @RestController
    @RequestMapping("/api/users")
    public class UserController {

        @GetMapping("/{id}")
        public UserDTO getUserById(@PathVariable Long id) {
            // Simulate fetching user details from a database
            return new UserDTO.Builder()
                    .username("johndoe")
                    .email("john.doe@example.com")
                    .role("ADMIN")
                    .build();
        }
    }
    ```

    This approach ensures that the object returned from the API is constructed cleanly with only the necessary fields set.

??? example "Alternative Ways"

    **Telescoping Constructors**
    Multiple overloaded constructors for different parameter combinations but Not ideal for readability and maintainability.

    ```java
    public Car(String make, String model) { ... }
    public Car(String make, String model, String color) { ... }
    public Car(String make, String model, String color, int year) { ... }
    ```

    **Setter Methods**
    Useful for mutable objects but doesn’t guarantee immutability but less readable when constructing objects with many attributes.

    ```java
    Car car = new Car();
    car.setMake("Tesla");
    car.setModel("Model S");
    car.setColor("Red");
    ```

## **Summary**

The Builder Pattern is an elegant way to handle object creation, especially when dealing with many fields or optional parameters. It ensures code readability, immutability, and flexibility while avoiding the need for numerous constructors. However, it should be used only when necessary, as simple objects may not benefit from it.

!!! note
    In Spring Boot, the Builder Pattern can be effectively used for creating DTOs and other complex objects, improving both code readability and maintenance. This pattern fits well when dealing with REST API responses or configuration settings, ensuring your objects are built in a clear, consistent manner.

---