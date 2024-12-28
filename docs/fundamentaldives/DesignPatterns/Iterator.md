[//]: # (TODO:: Need to add examples in other Language )

# **Iterator**

The Iterator pattern is a behavioral design pattern that allows sequential access to elements of a collection without exposing its underlying structure. The goal is to provide a way to access the elements of an aggregate object (such as an array, list, or set) one by one, without needing to understand how the collection is implemented.


## **What ?**

Iterator is a behavioral design pattern that lets you traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).

!!! info "Key Components"
    - Iterator: An interface that defines methods for traversing elements (e.g., `hasNext()`, `next()`).
    - Concrete Iterator: A class implementing the `Iterator` interface for specific collections.
    - Aggregate: An interface that defines a method to return an iterator (e.g., `iterator()`).
    - Concrete Aggregate: A collection (like List, Set, or Custom Collection) that returns a concrete iterator for traversal.


## **When to Use ?**
- When you need to traverse a collection without exposing its internal structure.
- When the collection might be complex (e.g., trees, graphs) or needs to be accessed in multiple ways.
- When you want to ensure uniform traversal logic regardless of the type of collection (array, list, set, etc.).
- Helpful when designing read-only views over complex collections.
- If you need multiple ways to traverse the collection (e.g., forward, reverse, or filtered).


## **When Not to Use ?**
- If the collection is small and performance is critical, an iterator might introduce overhead.
- When direct access to elements is more efficient or easier (e.g., in simple arrays or lists).


## **Advantages**
- Separation of concerns the client doesn’t need to know the internal structure of the collection.
- Multiple traversal strategies can implement different types of iterators (e.g., reverse iterator).
- Simplifies collections makes it easier to work with complex structures like trees or graphs.


## **DisAdvantages**
- Performance overhead if not managed properly, iterators can add unnecessary overhead.
- Limited control over collection Iterators typically provide read-only access.
- Concurrency issues Modifying a collection while iterating over it may throw `ConcurrentModificationException`.


## **How to Implement ?**

??? example "Sample Example"

    Let's go through with a simple Java example of a custom iterator for a list of strings.

    ```java title="Defining the Iterator Interface"
    interface Iterator<T> {
        boolean hasNext();
        T next();
    }
    ```

    ```java title="Creating a Concrete Iterator"
    class NameIterator implements Iterator<String> {
        private String[] names;
        private int index;

        public NameIterator(String[] names) {
            this.names = names;
        }

        @Override
        public boolean hasNext() {
            return index < names.length;
        }

        @Override
        public String next() {
            if (this.hasNext()) {
                return names[index++];
            }
            return null;
        }
    }
    ```

    ```java title="Defining the Aggregate (Collection) Interface"
    interface NameCollection {
        Iterator<String> getIterator();
    }
    ```

    ```java title="Implementing the Concrete Aggregate (Collection)"
    class NameRepository implements NameCollection {
        private String[] names = {"John", "Alice", "Robert", "Michael"};

        @Override
        public Iterator<String> getIterator() {
            return new NameIterator(names);
        }
    }
    ```

    ```java title=" Usage Example in Java"
    public class IteratorPatternDemo {
        public static void main(String[] args) {
            NameRepository namesRepository = new NameRepository();
            Iterator<String> iterator = namesRepository.getIterator();

            while (iterator.hasNext()) {
                String name = iterator.next();
                System.out.println("Name: " + name);
            }
        }
    }
    ```

??? example "Using Java’s Built-in Iterators"
    Java already provides built-in iterators for its collection framework (`Iterator`, `ListIterator`, and `Spliterator`).

    ```java title="Example with Java’s Built-in Iterator"
    import java.util.ArrayList;
    import java.util.Iterator;
    import java.util.List;

    public class BuiltInIteratorExample {
        public static void main(String[] args) {
            List<String> names = new ArrayList<>();
            names.add("John");
            names.add("Alice");
            names.add("Robert");

            Iterator<String> iterator = names.iterator();
            while (iterator.hasNext()) {
                System.out.println("Name: " + iterator.next());
            }
        }
    }
    ```

??? example "Spring Boot Example"

    Spring Boot doesn’t explicitly use the **Iterator pattern** as part of its core framework, but it does utilize **Iterators** internally (e.g., when dealing with `ApplicationContext` beans or data access layers). You can integrate the **Iterator pattern** within Spring Boot to handle collections of objects such as configurations, database entities, or APIs.

    ```java title="Create a Model Class"
    public class Book {
        private String title;

        public Book(String title) {
            this.title = title;
        }

        public String getTitle() {
            return title;
        }
    }
    ```

    ```java title="Create a Repository to Hold Books"
    import java.util.ArrayList;
    import java.util.List;

    public class BookRepository {
        private List<Book> books = new ArrayList<>();

        public BookRepository() {
            books.add(new Book("Spring in Action"));
            books.add(new Book("Java 8 in Action"));
            books.add(new Book("Microservices Patterns"));
        }

        public Iterator<Book> getIterator() {
            return books.iterator();
        }
    }
    ```

    ```java title="Service to Fetch Books Using Iterator"
    import org.springframework.stereotype.Service;
    import java.util.Iterator;

    @Service
    public class BookService {
        private final BookRepository bookRepository = new BookRepository();

        public void printBooks() {
            Iterator<Book> iterator = bookRepository.getIterator();
            while (iterator.hasNext()) {
                Book book = iterator.next();
                System.out.println("Book: " + book.getTitle());
            }
        }
    }
    ```

    ```java title="Controller to Trigger the Book Listing"
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    @RequestMapping("/books")
    public class BookController {

        @Autowired
        private BookService bookService;

        @GetMapping
        public void listBooks() {
            bookService.printBooks();
        }
    }
    ```

    When you access `http://localhost:8080/books`, it will print the list of books using the iterator after running the application


## **Summary**
The Iterator pattern is a powerful way to decouple iteration logic from collections, ensuring a clean separation of concerns. It’s useful in Java projects where complex or multiple ways of traversal are required. When integrated into Spring Boot, the pattern can be applied to iterate over configurations, data models, or APIs efficiently.

---