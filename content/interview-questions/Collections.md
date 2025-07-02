Q: What is the difference between WeakHashMap and HashMap?
A: In a HashMap, the keys are stored using strong references. This means that as long as the HashMap itself is not garbage collected, its keys and values are not either, regardless of whether there are other references to those keys outside the HashMap. WeakHashMap stores keys using weak references, which means that if a key is no longer strongly referenced from elsewhere in the application and the only reference to it is in the WeakHashMap, it can be garbage collected. When a key is garbage collected, its entry is removed from the WeakHashMap automatically.

Q: Why doesn't Map extend the Collection interface?
A: The map interface follows a key/value pair structure whereas the Collection interface is a collection of objects which are stored in a structured manner with a specified access mechanism. The add(E e) method of the collection interface doesn't support the put(K, V) method of the map. 

Q: What is the difference between fail-fast and fail-safe Iterators?
A: Fail-fast iterators throw a ConcurrentModificationException when one thread is iterating over a collection object while another thread is modifying the collection by either adding, removing or modifying objects. They are fail-fast because they try to immediately throw an exception when failure occurs. Fail-safe iterator do not throw any exception if a collection is structurally modified by one thread while being iterated over another thread. This happens because they work on a clone of the collection instead of the original one.

Q: What is the difference between a synchronized collection and a concurrent one?
A: Synchronized collections offer global locking where every method call is synchronized on the same object which means only one thread can access the collection at any time. Concurrent collections use more sophisticated internal algorithms such as segmented or non-blocking locking to allow concurrent access by multiple thread. In general, synchronized collections are simpler and suitable for low-concurrency scenarios (due to possibility of performance bottlenecks) while concurrent collections are designed for high-concurrency environments. 

Q: What are the specific of the collections in the java.util package?
A: There are several implementations with their specifics:
1) ArrayList - resizable array implementation. Good for random access and when additions are made at the end.
	1) Get is O(1)
	2) Search is O(n)
	3) Insert/Delete at the end is O(1), O(n) if resizing is needed or it is at a random index
2) LinkedList - doubly-linked list implementation. Good for frequent insertions and deletions
	1) Get and Search are O(n)
	2) Insert/Delete at a known node is O(1) but O(n) if the node needs to be searched first
3) HashSet - backed by a hash map. Offers unique elements, no ordering.
	1) Search/Insert/Delete is O(1) average, O(n) worst-case
4) TreeSet - implements a red-black tree structure. Elements are ordered.
	1) Search/Insert/Delete is O(log n)
5) HashMap - key-value pairs, allows one null key and multiple null values. Order of elements is not guaranteed
	1) Get/Search/Insert/Delete is O(1) average, O(n) worst case
6) TreeMap - key-value pairs, based on a red-black tree. Sorted by natural ordering of keys or by a comparator
	1) Get/Search/Insert/Delete is O(log n)
7) Vector - similar to ArrayList but thread-safe. It is essentially a synchronized resizable array implementation
	1) Get is O(1)
	2) Search is O(n)
	3) Insert/Delete at the end is O(1), O(n) if resizing is needed or it is at random index
8) Stack - extends vector, LIFO stack of objects
	1) Search (for element position): O(n)
	2) Push/Pop (insert/delete at top): O(1) amortized, but O(n) if resizing is needed
9) Queue - FIFO queue of objects, can be implemented with a linked list
	1) Insert (offer) is O(1)
	2) Delete (poll) is O(1)
	3) Search (peek) is O(1)
10) PriorityQueue - unbounded priority queue based on a priority heap. Elements are ordered according to their natural ordering or by a Comparator provided at queue construction time.
	1) Insert (offer): O(log n)
	2) Delete (poll): O(log n)
	3) Search (peek): O(1)
11) ArrayDeque - resizable-array implementation. It is more efficient than Stack when used as a stack, and more efficient than LinkedList when used as a queue. Allows elements to be added or removed from both ends. Does not allow null elements.
	1) Get is not directly supported, but random access can be simulated with iteration or stream operations, which would be O(n).
	2) Insert at the beginning/end (offerFirst/offerLast): O(1) amortized, but might be slower (O(n) most likely) in case of resizing.
	3) Delete from the beginning/end (pollFirst/pollLast): O(1).
	4) Search (contains): O(n), as it would require linear traversal to find the element.

Q: What are the specifics of the collections in the java.util.concurrent package?
A: There are several implementations with their specifics:
1) ConcurrentHashMap - thread-safe without locking the whole structure. Good for high concurrency, with retrievals often reflecting the most recently completed update operations
	1) Get/Search/Insert/Delete is O(1) average, O(log n) for some operations under high contention
2) ConcurrentSkipListMap and ConcurrentSkipListSet - elements are ordered. Good for high concurrency with sorted elements.
	1) Get/Search/Insert/Delete is O(log n)
3) CopyOnWriteArrayList and CopyOnWriteArraySet - every mutation operation copies the entire underlying array. Good for scenarios where iterations outnumber mutations.
	1) Get/Search for ArrayList is O(1) and for ArraySet O(n)
	2) Insert/Delete is O(n) for both
4) LinkedBlockingQueue and ArrayBlockingQueue - both are bounded queues. LinkedBlockingQueue can optionally have a capacity limit while the ArrayBlockingQueue must have one. Good for producer-consumer scenarios
	1) Insert/Delete is O(1) for both
	2) Search is not directly supported
5) ConcurrentLinkedQueue - unbounded thread-safe queue based on linked nodes
	1) Insert/Delete: O(1)
	2) Search: Not directly supported, but iteration would be O(n)
6) ConcurrentLinkedDeque - unbounded concurrent deque based on linked nodes. Allows concurrent insertions and removals at both ends
	1) Insert/Delete at both ends: O(1)
	2) Search: Not directly supported, but iteration would be O(n)
7) LinkedBlockingQueue/Deque -  Deque/queue that additionally supports blocking operations that wait for the deque/queue to become non-empty when retrieving an element and wait for space to become available in the deque/queue when storing an element.
	1) Insert/Delete at both ends (for deque): O(1)
	2) Search: Not directly supported, but iteration would be O(n)
8) PriorityBlockingQueue - unbounded blocking queue that uses the same ordering rules as class PriorityQueue
	1) Insert (offer): O(log n)
	2) Delete (poll): O(log n)
	3) Search (peek): O(1)

Q: What are some additional, less frequently used data structures and their specifics?
A: There are several of those:
1) LinkedHashSet - maintains a doubly-linked list across all elements. Provides predictable iteration order. Maintains insertion-order. Not synchronized.
	1) Search/Insert/Delete: O(1) average, O(n) worst-case.
2) WeakHashMap - Entries will automatically be removed when its keys are no longer in ordinary use (no longer referenced).
	1) Get/Search/Insert/Delete: Similar to HashMap, O(1) average, O(n) worst case.
3) IdentityHashMap - uses reference equality when comparing keys and values
	1) Get/Search/Insert/Delete: Similar to HashMap, O(1) average, O(n) worst-case.
4) EnumSet/EnumMap - map/set implementation for use with Enum types. All elements must come from a single enum type.
	1) Insert/Delete/Search: Highly efficient, implementation-specific, but generally comparable to HashSet.
5) NavigableSet/NavigableMap  - usually implemented with TreeSet/TreeMap. Provides additional navigation methods beyond those of SortedSet/SortedMap. NavigableMap maintains ascending key order.
	1) Search/Insert/Delete: O(log n), as it is generally implemented by TreeSet.

Q: What is concurrency level in ConcurrentHashMap?
A: The concurrency level in a ConcurrentHashMap refers to the estimated number of concurrently updating threads. It's a parameter that affects the internal sizing of the map and aims to allow the specified number of threads to update the map concurrently without significant contention. This is achieved by partitioning the map into segment, each of which is essentially a separate hash table with its own lock. The concurrency level determines the number of these segments. The default concurrency level is 16 meaning up to 16 threads can concurrently write to the map with expectation of minimal contention but it is a soft guarantee. The concurrency level can be changed via the constructor of the map.

Q: Why is null not an allowed value in ConcurrentHashMap but it is in HashMap?
A: Null values would complicate the atomic operations provided by the map as methods like putIfAbsent() need to be able to distinguish between a key that is not present in the map and a key that is present but mapped to a null value. In general, it avoids the ambiguity of method return values and provides a better efficiency, especially in high concurrency scenarios.

Q: Can ConcurrentModificationException occur only in a multithreaded environment?
A: No, it can also happen in a single-threaded environment in the case of modifying a collection during iteration. 

Q: What is modCount in an ArrayList?
A: The modCount is a field used to keep track of the number of structural modifications (ones that change the size of the list or modify it in a way that could cause iterations in progress to yield incorrect results) made to the list. It is primarily used as the foundation for the fail-fast behavior of the iterators. When an iterator is created, it takes a snapshot of the modCount value and compares it against the current modCount after every relevant iterator operation. If it finds that the list has been structurally modified, it throws the ConcurrentModificationException.

Q: What sorting algorithms are used in Java?
A: For primitive types, Java uses a Dual-Pivot quicksort algorithm while for objects, the TimSort algorithm is used. It is a hybrid stable sorting algorithm derived from merge sort and insertion sort. 

Q: How are collisions in HashMap handled?
A: After a certain threshold, the linked list in the hash map is replaced with a binary tree. When converting to a binary tree, the hash code is used as a branching variable. Because tree nodes are twice the size of regular nodes, they are only used when the buckets contain enough nodes, when they become too small (due to removal or resizing), they are converted back to plain buckets. The change to a tree and then back to a linked list is defined by two static variables - TREEIFY_THRESHOLD and UNTREEIFY_THRESHOLD. This whole operation is done to increase the worst-case performance of the map from O(n) to O(log n).

Q: What is the difference between HashMap and HashTable?
A: There are several key differences between the data structures:
1) HashTable is synchronized while HashMap is not.
2) HashTable doesn't allow null keys or null values while HashMap allows one null key and any number of null values
3) HashTable's iterators are fail-safe and do not throw ConcurrentModificationException while HashMap's iterators are fail-fast so they do throw it
4) HashTable's performance is worse than HashMap's (due to the synchronized nature). 

Q: What is the difference between array and LinkedList?
A: An array is a fixed-size data structure which is used to store primitives and objects. Accessing an element in an array is a constant-time operation because arrays are indexed and elements are stored in contiguous memory location. The main drawback is that inserting or deleting elements from anywhere but the end of the array is costly since it requires shifting elements to maintain order, which makes these operations O(n) time complexity. LinkedLists are dynamic structures, consisting of nodes, where each node holds the data and a reference to the next node in the sequence. LinkedLists can only store objects, but they can also store wrappers for primitives. Accessing elements is a linear-time operation as the whole structure has to be traversed. LinkedLists excel in scenarios where frequent insertion and deletion is needed as these operations can generally be performed in constant O(1) time, assuming direct access to the node is present. 

Q: How does a HashMap work in Java? Describe its key components.
A: A HashMap is a key-value store which works on the principle of hashing, providing constant-time performance for basic operations (insertion, retrieval and deletion), assuming the hash function disperses the elements properly between buckets. The HashMap is an array of buckets where each bucket is used to store entries. To determine in which bucket an entry should be placed, the key's hash code is used. Since different keys can produce the same hash code or different hash codes can map to the same bucket, collisions can occur. HashMap resolves this by using linked lists (or balanced trees if too many items end up in the same bucket). Each bucket thus has a linked list (or tree) of entries. The HashMap also has a load factor (0.75 by default) which determines how full it can get before it is resized upward. 

Q: What is a binary search tree and how do we perform insertion/deletion on it?
A: Multipart question answer:
1) Description - A Binary Search Tree (BST) is a data structure that organizes elements in a binary tree format where each node has up to two children. It has the unique property that for any given nodes, all elements in the left subtree are less than the node, all elements in the right subtree are greater than the node. This enables efficient searching, insertion, and deletion operations, typically in logarithmic time complexity, assuming that the tree is balanced. 
2) Insertion - To insert a new value in the BST, we start at the root, compare the value of the root and the new value, if it is smaller, we move to the left child, if greater, we move to the right child. This process is continued recursively until an empty spot is found where the new value can be inserted as a leaf node.
3) Deletion - there are several cases which need to be covered here:
	1) leaf node - it is simply removed from the tree
	2) node with one child - the node is removed and replaced with its child, maintaining the BST property
	3) node with two children - we have to find the in-order successor (the smallest node in the right subtree) or the in-order predecessor (the largest in its left subtree), replace the value of the node to be deleted with the found predecessor/successor, and the delete the predecessor/successor node from its original place.

Q: What is a TreeMap and how is it different from HashMap?
A: TreeMap is a map implementation that keeps its entries sorted according to the natural ordering of its keys or by a comparator provided at creation time. The most significant difference between the TreeMap and HashMap is that the TreeMap maintains sorted order of its keys. The TreeMap is implemented using a Red-Black tree (self-balancing binary search tree) which ensures operations like get, put, and remove are guaranteed to be in O(log n) time while HashMap uses an array of linked lists or trees and provides O(1) complexity, assuming a proper hash function. The TreeMap also doesn't allow null keys (since it needs to compare keys) while the HashMap allows one null key and many null values.

Q: What is hashing and how are collisions handled?
A: Hashing is a technique used to map data of various sizes to data of a fixed size. The values returned by a hash function is called a hash code and in data structures it is used to quickly locate a data record given its search key. A hash function takes the input, computes an integer value (hash code), which is then used to determine where the data should be stored in the hash table. Ideally, a hash function distributes keys uniformly across the hash table, minimizing the chance of collisions. Collisions can occur when two different keys produce the same hash code, or different hash codes that map to the same index in the hash table. Since the hash table should only store one element in each slot, several techniques can be used to resolve this problem:
1) Chaining - each slot of the hash table can contain a linked list (or list of some sort) that stores all elements which hash to the same index. If a collision occurs, the new element is simply added to the list at that index. This allows the hash table to never fill up, but the time to search for an element might increase if many elements are hashed to the same slot
2) Open addressing - all elements are stored within the hash table itself. When a collision occurs, the hash table looks for the next empty slot according to a probing sequence and stores the element there. There are different probing sequences. The main challenge with this approach is dealing with cluster formations, where consecutive slots get filled, slowing down the search and insertion operations.
3) Double hashing - form of open addressing where a secondary hash function is used to determine the step size for probing whenever a collision occurs. This method helps in distributing the entries more uniformly across the table, reducing the likelihood of clustering and collisions.

Q: What are the advantages of using generics in collections?
A: There are several key advantages that enhance the language's type safety, readability, and reusability of code:
1) Type safety - generics enforce type checks at compile time, ensuring only the specified type of objects can be inserted into a collection. This catches mismatch errors early in development, reducing runtime errors. 
2) Elimination of class casting - before generics, you had to cast every object you read from a collection. With generics, you implicitly specify the type of elements stored in a collection, eliminating the need for casting when retrieving them. This makes the code more cleaner and readable, as well as helping to reduce errors.
3) Reusable Code - generics enable algorithms to be written in a generic way, making them independent of the specific types of objects they operate on.
4) Interoperability with Legacy Code - generics were designed to be compatible with older versions of Java without generics. This means that code written with generics can still operate with legacy code that does not use generics, providing a bridge between new and old codebases.
5) Readability - Generics make the code more readable by making it explicit what type of objects are stored in a collection. When you see a Collection<String>, you immediately know it's a collection of strings.

Q: Explain the concept of Immutable Collections in Java. How can you make a Collection immutable?
A: Immutable Collections in Java are collections that cannot be modified after their creation. Once an immutable collection is created, its content cannot be changed - no new elements can be added, existing elements cannot be removed, and one cannot change the elements already in the collection. After Java 9, we can use the List.of, Set.of, and Map.of for creating immutable collections. Google's Guava Library also provides a range of immutable collection types and builders which are efficient to use. 

Q: How do you balance a Binary Search Tree?
A: To balance a Binary Search Tree, we aim to maintain its property where the left subtree of a node contains only nodes with keys less than the node's key, and the right subtree only nodes with keys greater than the node's key, while ensuring the depth of the two subtrees of every node differs by no more than one. Rotations are the primary method for rebalancing a tree. A rotation on a node involves moving around its children and potentially its parent, to reduce the height of the tree or subtree. Single rotations address the case where the unbalanced node and its child are skewed in the same direction (LL or RR), while double rotations are used when they are skewed in opposite directions (LR or RL).

Q: What are different types of self-balancing Binary Search Trees and what are their specifics? 
A: There are several most popular kinds of BSTs:
1) AVL trees - self-balancing BSTs where the difference in heights between the left and right subtrees (balance factor) is kept to a maximum of one for all nodes. When this balance factor is violated due to an insertion or deletion, the tree performs rotations to rebalance itself. These rotations are single or double and are named as Left-Left (LL), Right-Right (RR), Left-Right (LR), and Right-Left (RL) rotations based on the unbalanced condition.
2) Red-Black Trees - red-black trees are a more general self-balancing BST. They maintain balance with an additional color property, where each node is either red or black. The tree maintains balance through rules that revolve around the coloring and re-coloring of nodes and performing rotations during insertions and deletions to maintain these rules. Red-Black Trees guarantee that the path from the root to the farthest leaf is no more than twice as long as the path from the root to the nearest leaf, ensuring O(log n) operations.
3) Splay Trees - splay trees are self-balancing BSTs that move the accessed node to the root of the tree through a series of rotations, known as splaying. This ensures frequently accessed elements can be accessed quickly again, optimizing the tree based on access patterns.

Q: What is the difference between Vector and ArrayList?
A: ArrayList is not synchronized, non-thread safe, it is faster (due to not being synchronized) and it is not a legacy class. The opposite is true for Vector (synchronized, thread-safe, slower, is a legacy class).

Q: Can you use any class as a key in a Map?
A: The class has to override the equals() and hashCode() methods and adhere to the rules associated with these methods for all instances. For the best performance, the class should be immutable as that will help with caching the hashCode value for better performance. The class being immutable will also ensure that the hashCode() and equals() methods are not changing in the future.

Q: How can you optimize the initial capacity and load factor of a HashMap for a specific use case?
A: To optimize the initial capacity and load factor of a `HashMap` for a specific use case, you need to adjust these parameters based on your expected usage patterns.
1) Initial Capacity: Choose an initial capacity that can accommodate the expected number of entries to minimize resizing operations. For example, if you expect 1000 entries, you can set the initial capacity to 2048 (the next power of 2 greater than 1000/0.75). This reduces the need for resizing and rehashing, which can be costly operations.
2) Load Factor: The default load factor of 0.75 is generally a good balance between time and space costs. Lowering the load factor (e.g., to 0.5) reduces the likelihood of collisions but increases memory usage. Conversely, increasing the load factor (e.g., to 0.9) saves memory but may increase the chances of collisions and therefore degrade performance.

Q: How does the computeIfAbsent method in the Map interface work and in what scenarios is it useful?
A: The `computeIfAbsent` method in the `Map` interface works by computing a value for a specified key if the key is not already associated with a value or is mapped to null. It takes a key and a mapping function as arguments. If the key is not present or its value is null, the mapping function computes a value, which is then associated with the key.
This method is particularly useful in scenarios where you need to ensure a default or computed value exists for a key. For example, in a multi-map where each key maps to a list of values, you can use `computeIfAbsent` to initialize the list if it doesn't already exist.

Q: Compare LinkedHashMap and HashMap. When would you use one over the other?
A: There are several main differences between the classes:
1) HashMap doesn't maintain any order of entries while LinkedHashMap maintains insertion order or access order of entries
2) HashMap provides better performance for unsorted data retrieval while LinkedHashMap is slightly slower due to ordering overhead
3) HashMap is suitable for general-purpose cases where order is not important while LinkedHashMap is suitable for scenarios where the order of entries is important

Q: What are the practical differences and use cases for EnumMap compared to other Map implementations?
A: EnumMap is specifically designed for use with enum keys. It is extremely fast, memory-efficient, and maintains the natural order of enum keys. It doesn't allow null keys and is also not synchronized. It is used when the keys are of an EnumType due to its performance and memory efficiency. EnumMap is ideal for scenarios where a fixed set of keys is known in advance, such as state machines, configuration settings, or feature flags.

Q: Explain the use of NavigableSet and NavigableMap. Provide examples where they are particularly useful.
A: NavigableSet extends SortedSet to provide navigation methods like lower, floor, ceiling, higher, pollFirst, and pollLast while NavigableMap extends SortedMap with navigation methods like lowerEntry, floorEntry, ceilingEntry, higherEntry, pollFirstEntry, and pollLastEntry. They are both useful for implementing range queries or when you need to navigate within a sorted set of elements/key-value pairs.

Q: What are SortedSet and SortedMap? How do they differ from NavigableSet and NavigableMap?
A: SortedSet and SortedMap are respectively a map and set that maintain the elements/keys in ascending order. They provide methods like comparator, subSet, subMap, headSet, headMap, tailSet, and tailMap. NavigableSet and NavigableMap extend SortedSet and SortedMap, offering richer navigation capabilities compared to their counterparts. 

Q: How does the performance of HashMap compare to TreeMap for large datasets?
A: HashMap provides average O(1) time complexity for get, put, and remove operations while TreeMap provides O(log n) time complexity for get, put, and remove operations, however the HashMap performance depends on the quality of the hash function and load factor. HashMap also does not maintain any order of keys while TreeMap maintains sorted order of keys using a Red-Black tree. For large datasets where order is not important, HashMap is generally more performant due to its constant time operations. For scenarios requiring ordered traversal or range queries, TreeMap is preferred despite its logarithmic time complexity.

Q: What are the performance implications of using ConcurrentHashMap in a high-concurrency environment?
A: ConcurrentHashMap is designed for concurrent access with minimal synchronization. It uses segmented locking to allow multiple threads to read and write concurrently, and provides better performance in high-concurrency environments compared to synchronized collections like Hashtable. The segmented locking allows better utilization of multi-core processors, resulting in higher throughput. Fine-grained locking reduces contention among threads, leading to improved performance under load. It may have slightly higher memory overhead due to additional concurrency control structures.

Q: Explain the difference between CopyOnWriteArrayList and ArrayList. When would you choose one over the other?
A: CopyOnWriteArrayList is a thread-safe variant of ArrayList. On each modification, it creates a new copy of the underlying array, making it suitable for scenarios where reads vastly outnumber writes, such as event listener lists. ArrayList is not thread-safe unless externally synchronized and is suitable for scenarios with frequent updates and lower concurrency requirements.

Q: What are best practices for choosing the appropriate collection type for a given problem?
A: 
1) We need to consider the access patterns - frequent insertions/deletions or random access
2) Concurrency requirements - single-threaded or multi-threaded
3) Ordering requirements - maintain order or sorted order
4) Memory usage and performance - large datasets or space efficiency




