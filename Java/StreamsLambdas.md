# **Streams and Lambdas** 

### 1. **Lambda Expressions**  
- **Syntax**: `(parameters) -> expression` or `(parameters) -> { statements }`
- **Purpose**: Enables functional programming by treating functions as first-class citizens.
- **Examples**:
  ```java
  List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
  names.forEach(name -> System.out.println(name));
  ```

### Key Concepts:

#### 1.1. **Functional Interfaces**  

A **functional interface** is an interface with **only one abstract method**. This is important because **lambda expressions** can be used to provide the implementation for these interfaces. 

#### Example of Functional Interface:
```java
@FunctionalInterface  // Optional but ensures the interface has only one abstract method.
interface MyFunction {
    int apply(int a, int b);  // Single abstract method
}
```

Now, when you want to use this interface, you **don’t need to create a class** and provide an implementation like before. Instead, you can use a **lambda expression** to quickly provide the logic.

#### Using Lambda with MyFunction:
```java
MyFunction addition = (a, b) -> a + b;  // Lambda expression for addition
System.out.println(addition.apply(5, 3));  // Output: 8
```

- **Explanation**:  
  - `(a, b) -> a + b` is the lambda expression.
  - It directly implements the `apply(int a, int b)` method of the `MyFunction` interface.

#### 1.2. **Method References**

A **method reference** is a shorthand way of writing a lambda when a **method already exists** that matches the lambda’s purpose. This makes the code more concise and readable.

#### Example with `forEach` and Method Reference:

Consider the following list of names:
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
```

You want to **print all names** using `forEach()`. You could do it with a lambda like this:
```java
names.forEach(name -> System.out.println(name));  // Lambda expression
```

Now, Java provides a shorthand: **Method Reference**. Since `System.out.println()` already matches the structure `(String) -> void`, you can write:
```java
names.forEach(System.out::println);  // Method reference
```

- **Explanation**:
  - `System.out::println` is a **method reference** to the `println()` method of `System.out`.
  - It behaves just like the lambda `name -> System.out.println(name)` but is cleaner.

---

### When to Use Method References?

Use **method references** when:
- A lambda calls an **existing method** directly without modifying the input.
- It improves **readability** of the code.

#### More Examples:
```java
// 1. Static method reference
Function<String, Integer> parse = Integer::parseInt;
System.out.println(parse.apply("123"));  // Output: 123

// 2. Instance method reference on an arbitrary object
List<String> words = Arrays.asList("one", "two", "three");
words.sort(String::compareToIgnoreCase);  // Sorts case-insensitively
```

---

### 2. **Streams API**  
- Introduced in **Java 8** to process collections in a declarative way.

#### Core Stream Operations:
1. **Creation**:
   ```java
   Stream<Integer> stream = Stream.of(1, 2, 3, 4);
   List<String> list = Arrays.asList("A", "B", "C");
   Stream<String> streamFromList = list.stream();
   ```

2. **Intermediate Operations** (return new streams, lazy evaluation):
   - **`filter()`**: Filters elements based on a predicate.
     ```java
     List<Integer> evenNumbers = stream.filter(n -> n % 2 == 0).toList();
     ```
   - **`map()`**: Transforms elements.
     ```java
     List<Integer> lengths = list.stream().map(String::length).toList();
     ```
   - **`sorted()`**: Sorts elements.
     ```java
     List<Integer> sortedList = stream.sorted().toList();
     ```

3. **Terminal Operations** (trigger computation):
   - **`forEach()`**: Iterates through elements.
     ```java
     list.stream().forEach(System.out::println);
     ```
   - **`collect()`**: Collects elements into a collection.
     ```java
     List<String> newList = list.stream().filter(s -> s.startsWith("A")).collect(Collectors.toList());
     ```
   - **`reduce()`**: Reduces the elements to a single result.
     ```java
     int sum = Stream.of(1, 2, 3, 4).reduce(0, Integer::sum);
     ```

4. **Parallel Streams**:
   - Used to process elements in parallel for better performance.
     ```java
     list.parallelStream().forEach(System.out::println);
     ```

---

### 3. **Examples Combining Streams and Lambdas**  
**Find the sum of even numbers**:
```java
int sumOfEvens = Stream.of(1, 2, 3, 4, 5, 6)
                       .filter(n -> n % 2 == 0)
                       .reduce(0, Integer::sum);
System.out.println(sumOfEvens);  // Output: 12
```

**Convert List of Strings to Uppercase**:
```java
List<String> upperCaseNames = list.stream()
                                  .map(String::toUpperCase)
                                  .collect(Collectors.toList());
```

**Group elements by length**:
```java
Map<Integer, List<String>> groupedByLength = list.stream()
                                                 .collect(Collectors.groupingBy(String::length));
```

---

### 4. **Best Practices**  
- Prefer **Streams** over loops for readability.
- Use **parallel streams** with caution (only when operations are stateless).
- Avoid side effects in **Intermediate Operations** (e.g., printing inside `map()`).

---