# **Issues with Locking - LiveLock**

Locking mechanisms in Java, while essential for ensuring thread safety in multithreaded applications, can introduce various issues if not used properly. 

In this article, we’ll explore how livelock occur, how to prevent them, and practical examples of various techniques to detect and resolve livelock.

## **What is Livelock ?**

In a livelock, multiple threads remain active but unable to make progress because they keep responding to each other’s actions. Unlike deadlock, where threads are stuck waiting for locks indefinitely, threads in a livelock keep changing their states in response to each other, but they fail to reach a final state or make useful progress.

!!! note "Key difference from deadlock"
    In deadlock, threads are blocked waiting for each other, while in livelock, threads are not blocked, but they keep releasing and reacquiring locks or changing states in a way that prevents progress.

---

## **Example of Livelock**

Consider two people trying to pick up a spoon to eat, but they keep yielding to each other in an attempt to be polite. Neither person can make progress because they’re constantly checking and responding to each other’s actions.

???+ example "Livelock Example"
    ```java
    class Spoon {
        private boolean isAvailable = true;

        public synchronized boolean pickUp() {
            if (isAvailable) {
                isAvailable = false;
                return true;
            }
            return false;
        }

        public synchronized void putDown() {
            isAvailable = true;
        }
    }

    public class LivelockDemo {
        public static void main(String[] args) {
            Spoon spoon = new Spoon();

            Thread person1 = new Thread(() -> {
                while (!spoon.pickUp()) {
                    System.out.println("Person 1: Waiting for spoon...");
                    Thread.yield();  // Yield control to other threads
                }
                System.out.println("Person 1: Picked up spoon!");
            });

            Thread person2 = new Thread(() -> {
                while (!spoon.pickUp()) {
                    System.out.println("Person 2: Waiting for spoon...");
                    Thread.yield();  // Yield control to other threads
                }
                System.out.println("Person 2: Picked up spoon!");
            });

            person1.start();
            person2.start();
        }
    }
    ```

    !!! note "Explanation"

        - Both threads (Person 1 and Person 2) are trying to pick up the spoon.
        - If the spoon is not available, they yield control to the other thread to be polite.
        - However, both threads keep yielding repeatedly, resulting in livelock—neither thread can proceed.

---

## **Causes of Livelock**

- Excessive Yielding or Giving Way: Threads are too polite and keep yielding to each other and In Java, using `Thread.yield()` repeatedly can lead to livelock.

- Conflicting Retrying Logic: Both threads retry in response to each other’s behavior, creating a circular dependency.

- Improper Design of Locking Mechanisms, Threads repeatedly release and reacquire locks without making useful progress.

- Poor Handling of Shared Resources, When shared resources are locked and unlocked too frequently, threads can repeatedly fail to acquire them.

---

## **How to Avoid Livelocks**

### **Use Timeouts for Locking**

Using timeouts helps threads avoid indefinite waiting. If a thread cannot acquire the lock within a certain time, it can stop trying or take an alternative path.

???+ example "Using `tryLock()` with Timeout"
    ```java
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.locks.ReentrantLock;

    class Spoon {
        private final ReentrantLock lock = new ReentrantLock();

        public boolean pickUp() throws InterruptedException {
            // Try to acquire the lock with a timeout
            return lock.tryLock(1, TimeUnit.SECONDS);
        }

        public void putDown() {
            lock.unlock();
        }
    }

    public class LivelockFixed {
        public static void main(String[] args) {
            Spoon spoon = new Spoon();

            Thread person1 = new Thread(() -> {
                try {
                    if (spoon.pickUp()) {
                        System.out.println("Person 1: Picked up spoon!");
                        spoon.putDown();
                    } else {
                        System.out.println("Person 1: Couldn't get the spoon in time.");
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            Thread person2 = new Thread(() -> {
                try {
                    if (spoon.pickUp()) {
                        System.out.println("Person 2: Picked up spoon!");
                        spoon.putDown();
                    } else {
                        System.out.println("Person 2: Couldn't get the spoon in time.");
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            person1.start();
            person2.start();
        }
    }
    ```

    !!! info "Why it works ?"
        If a thread fails to acquire the lock within 1 second, it backs off instead of trying indefinitely.


### **Use Back-off Strategies**

A back-off strategy makes threads wait for a random amount of time before retrying. This avoids a situation where two threads keep checking the same lock in sync.


???+ example "Back-off Strategy Example"
    ```java
    import java.util.Random;
    import java.util.concurrent.locks.ReentrantLock;

    class Spoon {
        private final ReentrantLock lock = new ReentrantLock();
        private final Random random = new Random();

        public boolean tryPickUp() {
            return lock.tryLock();
        }

        public void putDown() {
            lock.unlock();
        }

        public void backOff() throws InterruptedException {
            Thread.sleep(random.nextInt(100));  // Wait for a random time
        }
    }

    public class LivelockWithBackoff {
        public static void main(String[] args) {
            Spoon spoon = new Spoon();

            Thread person1 = new Thread(() -> {
                try {
                    while (!spoon.tryPickUp()) {
                        System.out.println("Person 1: Waiting...");
                        spoon.backOff();  // Wait before retrying
                    }
                    System.out.println("Person 1: Picked up spoon!");
                    spoon.putDown();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            Thread person2 = new Thread(() -> {
                try {
                    while (!spoon.tryPickUp()) {
                        System.out.println("Person 2: Waiting...");
                        spoon.backOff();  // Wait before retrying
                    }
                    System.out.println("Person 2: Picked up spoon!");
                    spoon.putDown();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });

            person1.start();
            person2.start();
        }
    }
    ```

    !!! info "Why it works ?"
        The random back-off time prevents threads from retrying in lockstep, avoiding livelock.

### **Avoid Excessive Yielding**

Frequent use of `Thread.yield()` can lead to livelock. Instead, use timeouts or back-off strategies to prevent threads from constantly giving way to each other.

### **Use Condition Variables**

Use `Condition` variables (available with `ReentrantLock`) to properly coordinate threads waiting on specific conditions.

???+ example "Using Condition Variables Example"
    ```java
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.ReentrantLock;

    class Spoon {
        private boolean isAvailable = true;
        private final ReentrantLock lock = new ReentrantLock();
        private final Condition spoonAvailable = lock.newCondition();

        public void pickUp() throws InterruptedException {
            lock.lock();
            try {
                while (!isAvailable) {
                    spoonAvailable.await();  // Wait until spoon is available
                }
                isAvailable = false;
            } finally {
                lock.unlock();
            }
        }

        public void putDown() {
            lock.lock();
            try {
                isAvailable = true;
                spoonAvailable.signal();  // Notify waiting thread
            } finally {
                lock.unlock();
            }
        }
    }
    ```

    !!! note "Why it works ?"
        Using condition variables ensures that only one thread proceeds when the spoon becomes available, avoiding busy-waiting and yielding.

---

## **Best Practices**

- Use Timeouts: Prevent indefinite waiting by setting time limits for acquiring locks.
- Back-off Strategies: Introduce random delays before retrying to avoid lockstep behavior.
- Avoid Yield Loops: Replace `yield()` with smarter coordination mechanisms like conditions.
- Use Fair Locks: Fair locks reduce the chance of thread contention leading to livelock.
- Monitor and Tune: Use tools like VisualVM or JConsole to monitor thread states and detect livelocks in production systems.

---

## **Summary**

Livelocks can be tricky to detect because threads remain active, but they fail to make meaningful progress. By using timeouts, back-off strategies, condition variables, and proper locking mechanisms.

---