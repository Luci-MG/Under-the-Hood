# **Streams and Lambdas** 

## **Lambda Expressions**  
Enables functional programming by treating functions as first-class citizens.

- **Syntax**: `(parameters) -> expression` or `(parameters) -> { statements }`
???+ example
      ```java
      List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
      names.forEach(name -> System.out.println(name));
      ```

### **Functional Interfaces**  

A functional interface is an interface with only one abstract method. This is important because lambda expressions can be used to provide the implementation for these interfaces. 

???+ example
    ```java title="Example functional interface"
    @FunctionalInterface  // Optional but ensures the interface has only one abstract method.
    interface MyFunction {
        int apply(int a, int b);  // Single abstract method
    }
    ```

    Now, when you want to use this interface, you **don’t need to create a class** and provide an implementation like before. Instead, you can use a **lambda expression** to quickly provide the logic.

    ```java title="Using Lambda with MyFunction"
    MyFunction addition = (a, b) -> a + b;  // Lambda expression for addition
    System.out.println(addition.apply(5, 3));  // Output: 8
    ```

    ???+ info "Explanation"
        - `(a, b) -> a + b` is the lambda expression.
        - It directly implements the `apply(int a, int b)` method of the `MyFunction` interface.

### **Method References**

A method reference is a shorthand way of writing a lambda when a method already exists that matches the lambda’s purpose. This makes the code more concise and readable.

???+ example "Example with `forEach` and Method Reference"
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

???+ info "Explanation"
      - `System.out::println` is a **method reference** to the `println()` method of `System.out`.
      - It behaves just like the lambda `name -> System.out.println(name)` but is cleaner.

Use **method references** when:

- A lambda calls an **existing method** directly without modifying the input.
- It improves **readability** of the code.


???+ example "More Examples"
      ```java
      // 1. Static method reference
      Function<String, Integer> parse = Integer::parseInt;
      System.out.println(parse.apply("123"));  // Output: 123

      // 2. Instance method reference on an arbitrary object
      List<String> words = Arrays.asList("one", "two", "three");
      words.sort(String::compareToIgnoreCase);  // Sorts case-insensitively
      ```

---

## **Streams API**  
Introduced in **Java 8** to process collections in a declarative way.

!!! info "Core Stream Operations"
    #### Creation
    ```java
    Stream<Integer> stream = Stream.of(1, 2, 3, 4);
    List<String> list = Arrays.asList("A", "B", "C");
    Stream<String> streamFromList = list.stream();
    ```

    #### Intermediate Operations (return new streams, lazy evaluation)
    ```java title="filter()"
    // Filters elements based on a predicate.
    List<Integer> evenNumbers = stream.filter(n -> n % 2 == 0).toList();
    ```

    ```java title="map()"
    // Transforms elements.
    List<Integer> lengths = list.stream().map(String::length).toList();
    ```

    ```java title="sorted()"
    // Sorts elements.
    List<Integer> sortedList = stream.sorted().toList();
    ```

    #### Terminal Operations (trigger computation)

    ```java title="forEach()"
    // Iterates through elements.
    list.stream().forEach(System.out::println);
    ```

    ```java title="collect()"
    // Collects elements into a collection.
    List<String> newList = list.stream().filter(s -> s.startsWith("A")).collect(Collectors.toList());
    ```

    ```java title="reduce()"
    // Reduces the elements to a single result.
    int sum = Stream.of(1, 2, 3, 4).reduce(0, Integer::sum);
    ```

    #### Parallel Streams
    ```java
    // Used to process elements in parallel for better performance.
    list.parallelStream().forEach(System.out::println);
    ```

---

## **Examples Streams/Lambdas**  
???+ example "Find the sum of even numbers"
      ```java
      int sumOfEvens = Stream.of(1, 2, 3, 4, 5, 6)
                            .filter(n -> n % 2 == 0)
                            .reduce(0, Integer::sum);
      System.out.println(sumOfEvens);  // Output: 12
      ```

???+ example "Convert List of Strings to Uppercase"
      ```java
      List<String> upperCaseNames = list.stream()
                                        .map(String::toUpperCase)
                                        .collect(Collectors.toList());
      ```

???+ example "Group elements by length"
      ```java
      Map<Integer, List<String>> groupedByLength = list.stream()
                                                      .collect(Collectors.groupingBy(String::length));
      ```

---

## **Best Practices**  
- Prefer **Streams** over loops for readability.
- Use **parallel streams** with caution (only when operations are stateless).
- Avoid side effects in **Intermediate Operations** (e.g., printing inside `map()`).

---