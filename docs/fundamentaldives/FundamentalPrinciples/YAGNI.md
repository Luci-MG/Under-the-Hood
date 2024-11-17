# **YAGNI Principle**

Always implement things when you actually need them, never when you just foresee that you might need them.

---

## **What ?**

**YAGNI** stands for **"You Aren’t Gonna Need It."** It is one of the core principles of **Extreme Programming (XP)** and **Agile development**. The principle advises developers **not to add any functionality or code until it is truly needed**. Essentially, YAGNI promotes simplicity and avoids speculative development.

---

## **Why Use ?**
- Reduces Complexity as unused code can make the codebase harder to maintain.
- Saves Time as developers avoid spending time on features that might never be used.
- Reduces Maintenance Costs as Every piece of code adds maintenance overhead.
- Improves Focus as Teams stay focused on current requirements.
- Prevents Waste as You avoid wasting resources on unused or unnecessary functionality.

---

## **When to Use ?**
- During the early stages of software development when requirements are still evolving.
- In Agile or lean development methodologies, where the focus is on delivering working software quickly and iteratively.
- When planning features or writing code always ask, “Do we need this now?” If not, avoid implementing it.

---

## **When Not to Use ?**
- When designing core architecture You still need some foresight to design scalable systems. For example, if your product will likely need scalability soon, adding some basic support might be necessary. A good architecture sometimes requires forward-thinking to avoid technical debt.
- When working with security, compliance, or performance In certain domains, adding some safeguards or optimizations upfront is critical.
- In libraries or frameworks Sometimes, developers of reusable components need to anticipate needs of others.
- In infrastructure planning Systems like databases and message queues need to be built for the scale they might soon reach.
- In performance-critical applications, you might need to optimize upfront rather than waiting for problems to arise.


---

## **How to Apply ?**

- Focus on immediate requirements Write only the code needed to solve the problem at hand.
- Iterate frequently in Agile environments, deliver small but functional increments.
- Review code regularly ensures developers are not adding speculative code.
- Practice Test Driven Development (TDD).

---

## **Advantages**
- Faster Development By focusing only on what is necessary, development cycles are shorter.
- Easier Maintenance Less code means fewer bugs and easier updates.
- Lower Costs, Saves time, resources, and effort.
- Improved Team Efficiency Developers stay focused on real requirements.

---

## **Simple Java Example**

Lets go through a simple example in Java to illustrate **YAGNI in practice**.

??? example "Without YAGNI Example"
    ```java title="Adding Unnecessary Functionality"
    public class UserService {
        
        // Current functionality: Retrieve a user by ID
        public User getUserById(int id) {
            // Logic to retrieve user
            return new User(id, "John Doe");
        }

        // Unnecessary feature: Speculating that we may need a "deleteUser" method in the future
        public void deleteUser(int id) {
            // Logic to delete user (unimplemented)
            System.out.println("User deleted: " + id);
        }

        // Another unnecessary feature: Thinking we might need email notifications
        public void sendEmailNotification(User user) {
            // Logic to send email (unimplemented)
            System.out.println("Email sent to: " + user.getEmail());
        }
    }

    class User {
        private int id;
        private String name;

        public User(int id, String name) {
            this.id = id;
            this.name = name;
        }

        public String getEmail() {
            return name.toLowerCase() + "@example.com";
        }
    }
    ```

    ???+ info "Explanation"
        - A `deleteUser()` method.
        - A `sendEmailNotification()` method.
        - These methods were **added prematurely**, based on an assumption that they **might** be needed. This is a violation of the **YAGNI principle**. 

??? example "Applying YAGNI" 
    ```java title="Only Implement What is Needed Now"
    public class UserService {

        // Only add the necessary method for now
        public User getUserById(int id) {
            // Logic to retrieve user
            return new User(id, "John Doe");
        }
    }

    class User {
        private int id;
        private String name;

        public User(int id, String name) {
            this.id = id;
            this.name = name;
        }
    }
    ```

    ???+ info "Explanation"
        - We only implement the **necessary functionality** retrieving a user by ID.
        - We avoid premature code like `deleteUser()` or `sendEmailNotification()`.
        - If, in the future, we do need a `deleteUser()` or notification feature, we can implement it **when it becomes necessary**.

---

## **Summary**
The **YAGNI principle** encourages developers to focus on **delivering only the required features** at a given point in time, avoiding speculative development that may never be used. This approach fosters **simplicity, maintainability, and efficiency** in the codebase. However, it should be applied carefully there are scenarios (like architecture or security) where anticipating needs is necessary, When used properly, YAGNI helps teams build better software, faster, and with fewer headaches down the line.

---