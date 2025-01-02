# **Java Collections Framework**

## **Categories of Collections**
- List: Ordered, allows duplicates.
- Set: Unordered, no duplicates.
- Queue/Deque: FIFO or LIFO order.
- Map: Stores key-value pairs.
- Utility Collections: Collections with special behavior, eg: `Collections.synchronizedList()`.


## **List**

### **ArrayList**
A resizable array, fast random access. It's backed by Array, When random access is needed and insertions are rare you can use this.
  
Operations & Complexities

- Access by Index: `O(1)`  
- Insertion (end): `O(1)` (amortized)  
- Insertion (middle): `O(n)`  
- Space Complexity: `O(n)`  

Thread Safety:  Not synchronized, use `Collections.synchronizedList()` for thread safety.

??? example "ArrayList Example"
      ```java
      List<String> list = new ArrayList<>();
      list.add("Apple");
      list.get(0);  // Fast access
      ```

### **LinkedList**
A Doubly linked list, better at frequent insertions and deletions. It's backed by Doubly Linked List, When insertion/deletion in the middle is frequent you can use this.

Operations & Complexities

- Access by Index: `O(n)`  
- Insertion/Deletion: `O(1)` (at head or tail)  
- Space Complexity: `O(n)`

Thread Safety: Not synchronized.

??? example "LinkedList Example"
      ```java
      List<String> list = new LinkedList<>();
      list.add("Banana");
      list.addFirst("Apple");  // O(1) insertion at head
      ```


## **Set**

### **HashSet**
It's Unordered, no duplicates, backed by Hash Table. You can use this When you need fast lookups and no duplicates.

Operations & Complexities

- Add/Remove/Contains: `O(1)` (on average)  
- Space Complexity: `O(n)`

Thread Safety: Not synchronized, use `Collections.synchronizedSet()`.

??? example "HashSet Example"
      ```java
      Set<String> set = new HashSet<>();
      set.add("Cat");
      set.add("Dog");
      ```

### **LinkedHashSet**
This maintains insertion order, backed by a Hash Table + Linked List. You can use this when you need order-preserving behavior.

Operations & Complexities: Same as HashSet (`O(1)` operations) but with slightly higher overhead due to linked list maintenance.

??? example "LinkedHashSet Example"
      ```java
      Set<String> set = new LinkedHashSet<>();
      set.add("Apple");
      set.add("Banana");
      ```


### **TreeSet**
A Sorted set, backed by Red-Black Tree, When you need sorted data.

Operations & Complexities

- Add/Remove/Contains: `O(log n)`  
- Space Complexity: `O(n)`

Thread Safety: Not synchronized.

??? example "TreeSet Example"
      ```java
      Set<Integer> set = new TreeSet<>();
      set.add(5);
      set.add(1);  // Sorted automatically
      ```


## **Queue/Deque**

### **PriorityQueue**
Elements are ordered based on their natural order or a custom comparator. It's backed by Binary Heap, When priority-based retrieval is needed.

Operations & Complexities

- Insertion: `O(log n)`  
- Access (peek): `O(1)`  
- Remove (poll): `O(log n)`  

??? example "PriorityQueue Example"
      ```java
      Queue<Integer> queue = new PriorityQueue<>();
      queue.add(10);
      queue.add(5);  // 5 will be at the top
      ```

### **ArrayDeque**
Resizable-array-backed deque, allows adding/removing from both ends, When you need both stack and queue operations.

Operations & Complexities

- Add/Remove (head/tail): `O(1)`  
- Space Complexity: `O(n)`

??? example "ArrayDeque Example"
      ```java
      Deque<String> deque = new ArrayDeque<>();
      deque.addFirst("First");
      deque.addLast("Last");
      ```


## **Map**

### **HashMap**
Stores key-value pairs, backed by Hash Table, Fast lookups for key-value pairs.

Operations & Complexities

- Get/Put/Remove: `O(1)` (average)  
- Space Complexity: `O(n)`

Thread Safety: Not synchronized, use `ConcurrentHashMap` for thread-safe operations.

??? example "HashMap Example"
      ```java
      Map<String, Integer> map = new HashMap<>();
      map.put("Apple", 1);
      map.put("Banana", 2);
      ```

### **LinkedHashMap**
Maintains insertion order, backed by Hash Table + Linked List, When ordering of entries matters.

??? example "LinkedHashMap Example"
      ```java
      Map<String, Integer> map = new LinkedHashMap<>();
      map.put("Apple", 1);
      map.put("Banana", 2);  // Maintains insertion order
      ```

### **TreeMap**
Sorted map, backed by Red-Black Tree, When you need a sorted key-value store.

Operations & Complexities: Get/Put/Remove: `O(log n)`  

??? example "TreeMap Example"
      ```java
      Map<Integer, String> map = new TreeMap<>();
      map.put(3, "Three");
      map.put(1, "One");  // Sorted by key
      ```


## **Synchronized Collections**

**Synchronized Wrappers:** Use `Collections.synchronizedList()` or `Collections.synchronizedSet()` to make collections thread-safe.

??? example "Collections Synchronized Wrapper Example"
      ```java
      List<String> list = Collections.synchronizedList(new ArrayList<>());
      ```

**Concurrent Collections:** Use `ConcurrentHashMap`, `CopyOnWriteArrayList`, or `BlockingQueue` for better thread-safe alternatives.


## **Summary**

| Collection         | Type          | Backed By               | Access Time     | Insertion Time      | Deletion Time | Thread Safety | Use Case                            |
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