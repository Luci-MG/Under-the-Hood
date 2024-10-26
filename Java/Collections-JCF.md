# **Java Collections Framework**

### **Categories of Collections**
1. **List**: Ordered, allows duplicates.
2. **Set**: Unordered, no duplicates.
3. **Queue/Deque**: FIFO or LIFO order.
4. **Map**: Stores key-value pairs.
5. **Utility Collections**: Collections with special behavior, e.g., `Collections.synchronizedList()`.

---

## **1. List Implementations**

### **1.1 ArrayList**
- **Description**: A resizable array, fast random access.
- **Backed By**: **Array**
- **Use Case**: When **random access** is needed and insertions are rare.
  
#### **Operations & Complexities**
- **Access by Index**: `O(1)`  
- **Insertion (end)**: `O(1)` (amortized)  
- **Insertion (middle)**: `O(n)`  
- **Space Complexity**: **`O(n)`**  

#### **Thread Safety**:  
- **Not synchronized**, use `Collections.synchronizedList()` for thread safety.

#### **Code Example:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.get(0);  // Fast access
```

---

### **1.2 LinkedList**
- **Description**: Doubly linked list, better at frequent insertions and deletions.
- **Backed By**: **Doubly Linked List**
- **Use Case**: When **insertion/deletion** in the middle is frequent.

#### **Operations & Complexities**
- **Access by Index**: `O(n)`  
- **Insertion/Deletion**: `O(1)` (at head or tail)  
- **Space Complexity**: **`O(n)`**

#### **Thread Safety**:  
- **Not synchronized**.

#### **Code Example:**
```java
List<String> list = new LinkedList<>();
list.add("Banana");
list.addFirst("Apple");  // O(1) insertion at head
```

---

## **2. Set Implementations**

### **2.1 HashSet**
- **Description**: Unordered, no duplicates, backed by **Hash Table**.
- **Use Case**: When you need **fast lookups** and no duplicates.

#### **Operations & Complexities**
- **Add/Remove/Contains**: `O(1)` (on average)  
- **Space Complexity**: **`O(n)`**

#### **Thread Safety**:  
- **Not synchronized**, use `Collections.synchronizedSet()`.

#### **Code Example:**
```java
Set<String> set = new HashSet<>();
set.add("Cat");
set.add("Dog");
```

---

### **2.2 LinkedHashSet**
- **Description**: Maintains **insertion order**, backed by a **Hash Table + Linked List**.
- **Use Case**: When you need **order-preserving** behavior.

#### **Operations & Complexities**
- Same as **HashSet** (`O(1)` operations) but with **slightly higher overhead** due to linked list maintenance.

#### **Code Example:**
```java
Set<String> set = new LinkedHashSet<>();
set.add("Apple");
set.add("Banana");
```

---

### **2.3 TreeSet**
- **Description**: Sorted set, backed by **Red-Black Tree**.
- **Use Case**: When you need **sorted data**.

#### **Operations & Complexities**
- **Add/Remove/Contains**: `O(log n)`  
- **Space Complexity**: **`O(n)`**

#### **Thread Safety**:  
- **Not synchronized**.

#### **Code Example:**
```java
Set<Integer> set = new TreeSet<>();
set.add(5);
set.add(1);  // Sorted automatically
```

---

## **3. Queue and Deque Implementations**

### **3.1 PriorityQueue**
- **Description**: Elements are ordered based on their **natural order** or a **custom comparator**.
- **Backed By**: **Binary Heap**
- **Use Case**: When **priority-based retrieval** is needed.

#### **Operations & Complexities**
- **Insertion**: `O(log n)`  
- **Access (peek)**: `O(1)`  
- **Remove (poll)**: `O(log n)`  

#### **Code Example:**
```java
Queue<Integer> queue = new PriorityQueue<>();
queue.add(10);
queue.add(5);  // 5 will be at the top
```

---

### **3.2 ArrayDeque**
- **Description**: **Resizable-array-backed deque**, allows adding/removing from both ends.
- **Use Case**: When you need **both stack and queue operations**.

#### **Operations & Complexities**
- **Add/Remove (head/tail)**: `O(1)`  
- **Space Complexity**: **`O(n)`**

#### **Code Example:**
```java
Deque<String> deque = new ArrayDeque<>();
deque.addFirst("First");
deque.addLast("Last");
```

---

## **4. Map Implementations**

### **4.1 HashMap**
- **Description**: Stores **key-value pairs**, backed by **Hash Table**.
- **Use Case**: Fast lookups for key-value pairs.

#### **Operations & Complexities**
- **Get/Put/Remove**: `O(1)` (average)  
- **Space Complexity**: **`O(n)`**

#### **Thread Safety**:  
- **Not synchronized**, use `ConcurrentHashMap` for thread-safe operations.

#### **Code Example:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1);
map.put("Banana", 2);
```

---

### **4.2 LinkedHashMap**
- **Description**: Maintains insertion order, backed by **Hash Table + Linked List**.
- **Use Case**: When **ordering of entries** matters.

#### **Code Example:**
```java
Map<String, Integer> map = new LinkedHashMap<>();
map.put("Apple", 1);
map.put("Banana", 2);  // Maintains insertion order
```

---

### **4.3 TreeMap**
- **Description**: Sorted map, backed by **Red-Black Tree**.
- **Use Case**: When you need a **sorted key-value store**.

#### **Operations & Complexities**
- **Get/Put/Remove**: `O(log n)`  

#### **Code Example:**
```java
Map<Integer, String> map = new TreeMap<>();
map.put(3, "Three");
map.put(1, "One");  // Sorted by key
```

---

## **5. Synchronized Collections and Thread Safety Issues**

1. **Synchronized Wrappers**:  
   Use `Collections.synchronizedList()` or `Collections.synchronizedSet()` to make collections thread-safe.
   ```java
   List<String> list = Collections.synchronizedList(new ArrayList<>());
   ```

2. **Concurrent Collections**:  
   Use **`ConcurrentHashMap`**, **`CopyOnWriteArrayList`**, or **`BlockingQueue`** for better thread-safe alternatives.

---

## **Space and Time Complexity Table**

| **Collection**         | **Type**          | **Backed By**               | **Access Time**     | **Insertion Time**      | **Deletion Time** | **Thread Safety** | **Use Case**                            |
|------------------------|-------------------|-----------------------------|---------------------|-------------------------|------------------|------------------|-----------------------------------------|
| ArrayList              | List              | Resizable Array             | O(1)                | O(1) (amortized)        | O(n)             | No               | Fast random access                      |
| LinkedList             | List              | Doubly Linked List          | O(n)                | O(1)                    | O(1)             | No               | Frequent insertions/deletions           |
| HashSet                | Set               | Hash Table                  | -                   | O(1)                    | O(1)             | No               | Unique elements                         |
| LinkedHashSet          | Set               | Hash Table + Linked List    | -                   | O(1)                    | O(1)             | No               | Unique elements with insertion order   |
| TreeSet                | Set               | Red-Black Tree              | -                   | O(log n)                | O(log n)         | No               | Sorted elements                         |
| PriorityQueue          | Queue             | Binary Heap                 | -                   | O(log n)                | O(log n)         | No               | Priority-based retrieval                |
| ArrayDeque             | Deque             | Resizable Array             | -                   | O(1)                    | O(1)             | No               | Both stack and queue operations        |
| HashMap                | Map               | Hash Table                  | -                   | O(1)                    | O(1)             | No               | Fast key-value lookups                  |
| LinkedHashMap          | Map               | Hash Table + Linked List    | -                   | O(1)                    | O(1)             | No               | Key-value lookups with insertion order |
| TreeMap                | Map               | Red-Black Tree              | -                   | O(log n)                | O(log n)         | No               | Sorted key-value pairs                  |
| ConcurrentHashMap      | Concurrent Map    | Segmented Hash Table        | O(1)                | O(1)                    | O(1)             | Yes              | Thread-safe map                         |
| CopyOnWriteArrayList   | Concurrent List   | Array                       | O(n)                | O(1)                    | O(1)             | Yes              | Thread-safe list                        |
| BlockingQueue          | Concurrent Queue  | Queue/Linked Nodes          | Depends on impl.    | O(1)                    | O(1)             | Yes              | Thread-safe queue                       |



---