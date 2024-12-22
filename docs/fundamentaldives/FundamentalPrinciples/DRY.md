[//]: # (TODO:: Need to add examples in other Language )

# **DRY Principle**

## **What ?**

The DRY Principle stands for *Don’t Repeat Yourself*. It is a fundamental software development principle aimed at reducing repetition of code and logic. The main idea is that duplication introduces potential risks, if you need to update logic in multiple places, you might forget some, leading to bugs and inconsistencies. When applied well, it improves maintainability, scalability, and clarity, yupp something that lot of codebases misses.

!!! tip ""
    "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system."

In other words, the DRY principle encourages developers to write modular, reusable code and avoid duplicating the same functionality in multiple places.
It encourages us to minimize redundancy and write code that does one thing well, making our lives (and the lives of those who maintain our code) much easier.


## **When to Use ?**
- When similar code is being reused across multiple places.
- When there is duplication of logic (same logic split across functions, classes, or layers).
- When code updates require changing multiple parts of the program at once.
- In design patterns or utility classes Extract common behavior to avoid redundancy.
- Database queries, APIs, and validations Avoid repeated queries or repeated validation code.


## **Why to Use ?**

- Improved Maintainability with no duplication, you only need to update code in one place, reducing the risk of errors.
- Reduced Bug Risks When code is repeated, a bug in one place may go unnoticed in others. DRY ensures consistent logic across the codebase.
- Better Code Readability as redundant code makes it hard to focus on the real logic. Extracting common behavior enhances clarity.
- Easier Refactoring as DRY simplifies the process of changing logic. Code is modular and avoids unnecessary redundancy.
- Encourages Reusability using DRY often leads to utility classes, helper methods, or shared components that can be reused across projects.


## **When Not to Use ?**

- Over-abstracting can lead to tightly coupled code. If two pieces of code look similar but serve different purposes, it might not be a good idea to merge them.
- Don't in micro-optimizations, If extracting code results in decreased performance or introduces complexity, it’s better to keep things simple.


## **How to Use Example ?**

Let's cover an example where we apply DRY principle to refactor duplicated logic into reusable.

??? danger "Without DRY (Code Duplication) Example"
    ```java
    public class OrderService {

        public void placeOrder(int productId, int quantity) {
            if (quantity <= 0) {
                throw new IllegalArgumentException("Quantity must be greater than zero.");
            }
            // Logic to place order
            System.out.println("Order placed for product: " + productId);
        }

        public void cancelOrder(int orderId) {
            if (orderId <= 0) {
                throw new IllegalArgumentException("Order ID must be greater than zero.");
            }
            // Logic to cancel order
            System.out.println("Order cancelled: " + orderId);
        }

        public void updateOrder(int orderId, int quantity) {
            if (orderId <= 0) {
                throw new IllegalArgumentException("Order ID must be greater than zero.");
            }
            if (quantity <= 0) {
                throw new IllegalArgumentException("Quantity must be greater than zero.");
            }
            // Logic to update order
            System.out.println("Order updated with new quantity: " + quantity);
        }
    }
    ```

    ???+ info "Explanation" 
        - The validation logic is duplicated across multiple methods (`placeOrder`, `cancelOrder`, and `updateOrder`).
        - If we want to change the validation rule, we need to update all the occurrences.
        - This is a maintenance burden and prone to bugs.



We can extract the common logic into a reusable private method to apply the DRY principle.

??? success "With DRY (Refactored Code) Example"
    ```java
    public class OrderService {

        public void placeOrder(int productId, int quantity) {
            validateQuantity(quantity);
            // Logic to place order
            System.out.println("Order placed for product: " + productId);
        }

        public void cancelOrder(int orderId) {
            validateOrderId(orderId);
            // Logic to cancel order
            System.out.println("Order cancelled: " + orderId);
        }

        public void updateOrder(int orderId, int quantity) {
            validateOrderId(orderId);
            validateQuantity(quantity);
            // Logic to update order
            System.out.println("Order updated with new quantity: " + quantity);
        }

        // Reusable validation methods
        private void validateOrderId(int orderId) {
            if (orderId <= 0) {
                throw new IllegalArgumentException("Order ID must be greater than zero.");
            }
        }

        private void validateQuantity(int quantity) {
            if (quantity <= 0) {
                throw new IllegalArgumentException("Quantity must be greater than zero.");
            }
        }
    }
    ```

    ???+ info "Explanation"
        - We removed the duplicated validation code and moved it into **dedicated reusable methods** (`validateOrderId` and `validateQuantity`).
        - Now, if we need to change the validation logic, we only have to update it in one place.
        - The code is easier to read, maintain, and extend.


## **Summary**

The DRY principle ensures that code duplication is minimized for easier maintenance and improved consistency. In our example, we extracted common validation logic to private methods, adhering to DRY and making the code more maintainable. However, always be careful to avoid over-abstraction, as not all code repetition is bad. The goal is to achieve a balance between simplicity and reusability.

---