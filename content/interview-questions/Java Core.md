Q: Why is Java not 100% Object-oriented?
A: 1) Because it supports primitive data types (int, byte, short, long, float, double, char and boolean) which are not objects. 2) Java allows the use of static methods and variables which can be accessed without creating an instance of the class in which they are defined. 3) Control structures like for/while loops and conditional statements like if/switch could also be implemented via method calls on objects rather than language specific syntax.

Q: Why are pointers not used in Java?
A: Because of the existence of the JVM which is responsible for implicit memory allocation, removing the direct access to the memory from the user. The concept increases the safety and decreases the complexity of the program.

Q: What is JIT compiler in Java?
A: JIT stands for Just-In-Time compiler and it is a crucial component of the Java Runtime Environment which improves the performance of Java applications by compiling bytecode (the intermediate representation of Java code compiled by the Java compiler) into native machine code at runtime. The JIT compiler generates native code for the specific hardware and operating system on which the application is running, taking advantage of platform-specific features and instructions to enhance performance.

Q: Why is String immutable in Java?
A: 
1) Mainly because of security reasons as it is used in different areas like file paths, networking connections, database connection, etc. Having immutable string allows you to be secure and safe because no one can change the reference of the string once it is created. 
2) The string pool would also be unable to work unless it is immutable as shared references could be changed from anywhere.

Q: What is string pool?
A: The String Intern pool is a special area in the Java heap memory intended to store unique string literals. This concept is used to optimize memory usage and improve performance when dealing with strings. When creating a new string, the JVM checks if it already exists in the memory pool and if so, points the reference towards that string. 

Q: What is a marker interface?
A: An empty interface which is used to provide additional metadata to the compiler and denoting that the classes which implement this have something to do with, for example Cloning, Serializing, etc.

Q: What is method overriding?
A: This happens when a subclass or child class has the same method as the parent class. It provides a particular implementation of a method declared by one of the parent classes. 

Q: Can you override a private or static method in Java?
A: You cannot override neither a private nor a static method. The private method cannot be overridden because it is not accessible in the subclass. If both a parent and child class have a static method with the same signature then the method from the parent will be "hidden". This is different from method overriding because the overriding is resolved at runtime, based on the object's class while the hidden methods are resolved at compile time, using the reference type to determine which one to call instead of the class the object it points to.

Q: Does the "finally" keyword always execute? 
A: It doesn't execute only when the "System.exit()" function is explicitly called or the system crashes.

Q: What methods does the "Object" class have?
A: The "Object" class is the parent of all other classes and it has the following methods:
1) protected Object clone() throws CloneNotSupportedException - creates and returns a copy of the object.
2) public boolean equals (Object obj) - indicates whether another object is "equal to" this one.
3) protected void finalize() throws Throwable - called by the garbage collector on an object when garbage collection determines that there are no more references to this object.
4) public final Class getClass() - return the runtime class of an object
5) public int hashCode() - returns a hashcode value for the object
6) public String toString() - return a string representation of the object
7) public final void notify()
8) public final void notifyAll()
9) public final void wait()
10) public final void wait(long timeout)
11) public final void wait(long timeout, int nanos)

Q: How can you make a class immutable?
A: 6 steps have to be performed:
1) Declare the class as final so it can't be extended. 
2) Make all fields private so direct access is not allowed. 
3) Don't provide setter methods for fields.
4) Make all mutable fields final so the value can be assigned only once.
5) Initialize all fields via a constructor performing a deep copy. 
6) Perform cloning of objects in the getter methods to return a copy. rather than returning an actual object reference.

Q: What is a singleton class in Java and how can we make a class singleton?
A: Singleton is a class which can only have one instance created at any given time, in one JVM. To make a class a singleton we need to 1) have a private constructor and 2) have a static method returning a reference to the instance of the class, creating it if it doesn't already exist.

Q: What is an exception?
A: Exception is an abnormal condition which occurs during the execution of a program and disrupts the normal flow of the program. If not handled appropriately it can cause the program to terminate abruptly.

Q: How are exceptions handled in Java?
A: Using a try-catch-finally block. The code which can throw an exception is put in the try block, the code which handles the exception is put inside the catch block and inside the finally block is where usually the clean up activities are stored. The finally block executes regardless of whether an exception was caught or not.

Q: What is the hierarchy of exceptions in Java?
A: The main parent class is "Throwable", from there we have two types of classes - "Error" or "Exception". The "Exception" class is split in two more subclasses - "Checked Exception" (which is handled at compile time) or "Unchecked Exception" (which is handled at run time). 

Q: What is the difference between an Error and an Exception?
A: There are several main differences:
1) We can recover from an exception by using a try-catch block or similar structure while it is not possible to recover from an error.
2) The compiler will know about checked exceptions while it will not have any knowledge about errors (they are unchecked).
3) Exceptions are related to the application while errors are related to the environment where the application is running.

Q: Are remaining statements in a try block executed after an exception has occurred?
A: No, if an exception occurs at any point, all statements, after the statement which threw the exception, will not execute. The flow goes directly to the catch/finally block.

Q: What is an unreachable catch block error?
A: This error comes up when you try to catch a super class exception first, before a child class exception. That is why the try-catch blocks should always be ordered from the most specific ones to the most general ones.

Q: What is a multi-catch block?
A: When we have a structure where on one row we are catching multiple types of exceptions. This can be used for a cleaner structure when different types of exceptions will be handled in the same way. 
Example: try { } catch (NullPointerException | SQLException ex) { }

Q: What is the difference between final, finally, and finalize in Java?
A: **final** is a keyword used to apply restrictions on the class, method, and variable. The final class can't be inherited, the final method can't be overridden and the final variable can't be changed. **finally** is used with the try-catch block to provide a statement that will always get executed even if an exception arises. It is usually used to close resources. **finalize** is used to perform clean up processes on an object before it is garbage collected.

Q: What is "Comparator" and "Comparable" in Java?
A: The comparable interface is used to define the natural ordering of objects in a class. It is a generic interface which when implemented, the class must provide an implementation for the compareTo(T o) method to specify how instances of the class should be ordered relative to each other. The comparator interface is used to define a custom ordering of objects of a class and provides a single method compare(T o1, T o2). Unlike "Comparable", "Comparator" allows multiple definitions of ordering for a class without modifying the class's code. 

Q: What is pass by reference and pass by value and which paradigm does Java use?
A: Pass by reference means that the actual argument (direct memory address) is given to a function or method and hence any changes made inside a function or method will affect the passed object itself. Pass by value means that a copy of the original reference is passed to a method or function and hence any changes made to it will not be affect the original object. Java is a pass by value programming language, however, when we have a Java object which is passed to a method, changes made to it (such as setting a new value for a field) will affect the original object too as both the original object and the copy share the same memory address. Although that is true, it's not possible to change the reference of the object (making the Object = new Object()). 

Q: Can we reduce the visibility of a derived method (from a superclass) in Java?
A: No, we can't. If the superclass has a public method and we try to make override the inherited method with a private or protected modifier we will receive a compile time error that it is not possible.

Q: What are the access modifiers in Java and what are their specifics?
A: There are 4 types - **private**, **default**, **protected**, and **public**. Private means that the visibility is only present in the current class. Default means that the visibility is only present within the same package. Protected means that the visibility is present in the current package and through any child classes. Public means that the visibility is present everywhere

Q: What is a default method in an interface?
A: A default method in the interface is a method with a body that provides a default implementation. The class implementing the interface doesn't have to override this method to use its' functionality, however, if need be, it can be overridden.

Q: Can an interface have a static method and if yes, what is their use?
A: Yes, interfaces can have static methods which is similar to a default method in the sense that it has a body and every class implementing the interface will have access to this method. However, unlike the default methods, the static interface methods cannot be overridden by the implementing classes. It can be used in the case where we want all members implementing a certain interface to have access to a particular method without being able to change/override its' implementation from within. 

Q: What is multiple inheritance and does Java allow it?
A: Multiple inheritance means that one child will have 2 or more parent classes. Multiple inheritance is not allowed for classes in Java, however, it is possible to have it in an interface. The multiple inheritance is not possible for classes due to the ambiguity of which implementation is to be used, but as interfaces don't provide an implementation this problem doesn't exist.

Q: What is method overloading?
A: Method overloading is the ability of creating multiple methods with the same name but different parameters within a class.

Q: What is deep and shallow copying?
A: A shallow copy essentially means copying the reference of a certain object. For example, creating an int[] array = {1, 2, 3} and the creating a new int[] copy = array. Deep copying means actually creating a new object and copying over the values from the previous one. In the array example, that would mean looping over the int[] array and putting each of the values in the new int[] copy. 

Q: What are the 4 OOP principles?
A: Abstraction, Encapsulation, Inheritance, and Polymorphism.

Q: What is abstraction?
A: Hiding implementation details and thereby leaving only the essential ones. Abstraction can be achieved through the use of abstract classes (which cannot be instantiated) or interfaces (which unlike abstract classes support multiple inheritance, allowing multiple behaviors). 

Q: What is polymorphism?
A: The ability of an object to respond to the same method call in different ways. There are 2 primary types of polymorphism supported in Java - compile-time (static) and runtime (dynamic). To make an object polymorphic they have to extend another object, therefore all object in java are technically polymorphic (because all of them extend from the "Object" class).

Q: What are the types of polymorphism in Java and how are they achieved?
A: There's 2 primary types of polymorphism - compile-time (static), which is achieved through method overloading, and runtime (dynamic), which is achieved through method overriding. 

Q: Can you overload a method by changing only its' return type?
A: No, this is not possible, and it will be a compile-time error due to ambiguity (the compiler doesn't know which method should be called). 

Q: What is upcasting in Java?
A: This is the process of converting a reference of a subclass type to a reference of its superclass type. This allows a subclass object to be treated as an instance of any of its super classes, enabling polymorphic behavior.

Q: What is the concept of covariant return types in Java?
A: Covariant return types in Java refers to the ability of a method to return a type that is subclass of the type returned by the overridden or implemented method in the super class or interface. 

Q: What is encapsulation?
A: Encapsulation is the technique of wrapping the data variables and the code that acts upon them (methods) integrated as a single unit. Particularly in Java, this essentially means that we need to have the class variables private and provide public getter and setter methods to modify and view the variable values. 

Q: What is inheritance?
A: Inheritance is a mechanism that allows one class to inherit the properties (fields) and methods of another class. This enables code reusability and establishes a parent-child relationship between classes.

Q: How many types of inheritance does Java support?
A: Java supports 4 main types of inheritance for classes and one more type but only for interfaces:
1) Singe inheritance - which means 1 superclass has 1 child class
2) Multi-level inheritance - which means 1 superclass has 1 child class and then the child class can have children of its' own and this can be done on many levels (Animal -> Dog -> Labrador)
3) Hierarchical inheritance - which means a number of classes are derived from a single base superclass (Animal -> Dog/Fish/Cat)
4) Hybrid inheritance - which means that a structure is using more than 1 type of inheritance (mixed)
5) Multiple inheritance - this is not supported for classes in Java but it is possible to be used for interfaces. This means that a child can have more than 1 parent.

Q: What is aggregation and composition in Java?
A: Aggregation and composition are two forms of association that represent relationships between objects. Aggregation is a weaker form of association where object have their own lifecycle, and there is no owner - one object is part of another object, but both can exist independently. Composition is a stronger form of association with stricter ownership between the contained object and the container object. The contained object cannot exist independently of the container - if the container is destroyed, the contents are destroyed too.

Q: How many types of variables are there in Java?
A: In Java there are 3 types of variables, namely:
1) Instance variables - they are declared in a class but outside any method, constructor or block. They are also known as fields or member variables. They get default values according to their types (for example, 0 for integers, null for objects)
2) Local variables - they are declared inside a method, constructor or block and are only accessible within that method, constructor or block. They must be initialized before use. The lifetime of a local variable is limited to the execution of the method or block in which it is declared, it gets destroyed once the execution is completed.
3) Static (class) variables - they are declared with the static keyword within a class but outside any method, constructor, or block. Unlike instance variables which are unique to each instance of a class, static variables are common to all instances. There is only 1 copy of a static variable per class, regardless of how many objects are created. Like instance variables, they also get default values based on their types.

Q: What is the difference between JDK, JRE and JVM?
A: The JVM is the smallest unit which is contained within the JRE and is responsible for executing the .class bytecode executable generated by the javac compiler. The JRE is essentially a combination of the JVM and library classes, responsible for executing Java applications. The JDK is a combination of the JRE and development tools as it is used for the purpose of both running and developing Java applications. 

Q: What is the Java code lifecycle?
A: The Java code is first written in a .java source code file which is compiled by the javac compiler, generating the .class Java bytecode executable file. The JVM in association with the Just-in-Time compiler runs and manages that code within the JRE. 

Q: What is garbage collection in Java?
A: Garbage collection is an automatic process of looking at heap memory, identifying which objects are in use and which are not, and deleting the unused objects. An in use object is one that some part of the program still maintains a reference to.

Q: Where are objects stored in memory?
A: Local variables inside a method (including references to objects) are stored on the stack. When an object has fields that are references to other objects, these references are stored within the object itself, which resides in the heap. Static fields, even though being able to hold references to objects, are stored in the heap within the class's method area or a similar space designated for class metadata and static content. 

Q: Which part of the memory is involved in garbage collection?
A: The garbage collection scans the heap and removes the inactive objects to free up the memory. 

Q: Who manages the garbage collector?
A: The JVM controls the garbage collector and decides when it should be run. The garbage collector is run more often when the memory is running low. The garbage collection can be requested (System.gc()) but it is not guaranteed exactly when it will occur.

Q: Is garbage collector a foreground or background thread?
A: The garbage collector is a daemon/background thread which is started by the JVM. It stops when all non-daemon/foreground threads stop as well. 

Q: What is the process of garbage collection?
A: The garbage collection is done in two steps. The first one is marking - objects that are not referenced are identified and marked ready for garbage collection. The 2nd step is the deletion or deletion + compaction. Memory is compacted after the garbage collection deletes an object so that remaining objects are in contiguous blocks at the start of the heap memory. This is done to make it easier to allocate memory sequentially, after a chunk of allocated memory, for new objects in the heap. 

Q: What are some of the most commonly used JVMs by Oracle?
A: The most commonly used JVM by oracle is HotSpot JVM, which is part of the Oracle JDK. It supports various garbage collection algorithms that can be selected based on the needs of the application, including Serial, Parallel, Concurrent Mark Sweep and Garbage-First. HotSpot JVM also uses the JIT compiler. Another one is GraalVM which is a universal virtual machine designed to run application in multiple languages like Java, JavaScript, Python and others. It uses a GraalVM compiler which is a JIT compiler. GVM can be run standalone or in conjunction with HS JVM. Prior to being phased out in favor of HotSpot, Oracle JRockit was a proprietary JVM for server-side application. 

Q: What are most commonly used garbage collection strategies?
A: There are several most commonly used garbage collection strategies, namely:
1) Mark-and-sweep: The algorithm works in two phases - all live objects are identified by traversing object references from a set of roots and then all objects not marked as live in the heap are swept, reclaiming their memory
2) Copying: In this algorithm the heap is divided into two halves - the "from" and "to" space. Live objects are copied from the "from" space to the "to" space and then the "from" space is wiped in its entirety. This is efficient in scenarios with many short-lived objects but can be wasteful of memory because only half the heap is used at any time.
3) Generational: This strategy is based on the observation that most objects are short-lived. The heap is divided into generations: the young generation for newly created objects and the old generation for objects that have survived multiple garbage collection cycles. Young generation collections (minor GC) are performed more frequently and are usually faster because they deal with a smaller memory area and have fewer objects to check. Old generation collections (major GC) are less frequent but more comprehensive and time-consuming.
4) Incremental and Concurrent: These strategies are used to break down the GC process into smaller chunks or perform part of the GC work concurrently with the application: incremental GC breaks down the process into smaller steps and interleaves them with application execution to reduce pause times. Concurrent GC performs most GC work while the application threads are still running, significantly reducing pauses due to GC.
5) Garbage-first: G1 is a server-style garbage collector designed for multi-processor machines with large memories. It aims to provide high throughput with predictable pause times. The heap is divided into regions, and G1 collects garbage by first reclaiming regions with the least live data, hence the name "Garbage-First."

Q: What is a lambda expression?
A: Lambda expressions are anonymous functions (without name, return type and access modifier) which have one lambda (->) symbol. It is mainly used to inline implementation of a functional interface (interface with a single abstract method).

Q: What is a functional interface?
A: A functional interface in Java is an interface that contains exactly one abstract method. The functional interface can have multiple default or static methods, there is no limitation on that. It is advisable to mark them with @FunctionalInterface which doesn't change the behavior but serves as a documentation aid. 

Q: What is a method interface?
A: A method reference in Java is a shorthand notation of a lambda expression to call a method. Method references can be used in any context where a lambda expression can be used, provided that the method signature matches the target type's functional interface method signature.
There are 4 types of method references: static method, an instance method of a particular object, instance method of an arbitrary object of a particular type, constructor.

Q: What are predicates?
A: "Predicate" is a functional interface which represents a single argument function that returns a boolean value. It's a generic interface, meaning it works with any object type, and is primarily used for evaluating conditions (whether something is true or false). They are often used in filtering operations, such as filtering collections of objects based on a certain criteria. Example: 
Predicate<String> isLongerThan5 = str -> str.length() > 5; System.out.println(isLongerThan5.test("Hello")); // Output: false System.out.println(isLongerThan5.test("Hello, World!")); // Output: true

Q: What are functions?
A: "Function" is a functional interface introduced in Java 8. It represents a function that accepts one argument and produces a result. This interface is a part of Java's support for functional programming and is extensively used in lambda expressions and method references. Example: 
Function<String, Integer> lengthFunction = str -> str.length(); System.out.println(lengthFunction.apply("Hello, world!")); // Output: 13

Q: What are consumers?
A: "Consumer" is a functional interface which represents an operation that accepts a single input argument and returns no result. 
Examples:
Consumer<String> printConsumer = System.out::println; printConsumer.accept("Hello, Consumer!"); // Output: Hello, Consumer!
Consumer<String> consumer1 = System.out::println; Consumer<String> consumer2 = str -> System.out.println(new StringBuilder(str).reverse()); // Combine consumers to first print the string and then print its reverse. Consumer<String> combinedConsumer = consumer1.andThen(consumer2); combinedConsumer.accept("Java"); // Output: // Java // avaJ

Q: What are suppliers?
A: "Supplier" is a functional interface which represents an operation that supplies a result of a certain type but does not take any input. Suppliers are often used when you need to generate or supply values without any external input. Example:
Supplier<Double> randomSupplier = Math::random; Double randomNumber = randomSupplier.get(); // Generates a random number

Q: What are BiConsumer, BiFunction, BiPredicate and why is there no BiSupplier?
A: They are extensions of the consumer, function, and predicate functional interfaces, supporting operations on two inputs, providing more flexibility when working with functions, operations or conditions that require two parameters. BiSupplier doesn't exist due to the nature of the interface itself - it is designed to provide a single value of type "T" without taking any input. The concept of a "BiSupplier" would imply supplying two values, but this breaks away from the simplicity and single responsibility of the "Supplier" interface. When you need to supply two values, typically you would supply a single value of a type that encapsulates both, such as a "Pair" or a custom class.

Q: What is multitasking and what types are there?
A: Multitasking means performing two tasks at the same time. There are two types of multitasking - process-based and thread-based multitasking. Process-based multitasking means executing several during processes at the same time (running several apps on your OS). Thread-based multitasking or multithreading means running several different tasks simultaneously within the same process where each task is a separate independent part of the same program (a thread). 

Q: Why is multithreading better than multitasking?
A: Multitasking requires more overhead than multithreading due to several reasons: processes are heavyweight tasks that require their own separate address space, while threads are lighter weight, share the same address space and cooperatively share the heavyweight process. Inter-process communication is also expensive and limited - context switching from one process to another allows for better use of the CPU but it also consumes CPU time, which can impact the overall performance of the system, especially if the rate of context switching is high.  

Q: What is a Thread?
A: Threads are the smallest unit of execution within a process. Java threads allow concurrent execution of two or more parts of a program for maximum utilization of CPU resources. Each thread has its' own call stack, but can access shared data with other threads running in the same process. There are several ways we can create a thread:
1) extending the "Thread" class, overriding its' "run()" method and the creating an instance and calling the "start()" method
2) implementing the "Runnable" interface, pass an instance of the implementing class to a new "Thread" object and after that call "start()" on the Thread object.
All Java programs have at least one thread, known as the main thread, which is created by the JVM at the program's start, when the main() method is invoked with the main thread.

Q: What are the different types of threads?
A: There are 3 main types of threads:
1) User threads (foreground threads) - main threads that the application starts, which are high-priority and the JVM will wait to complete before terminating. 
2) Daemon threads (background threads) - low priority threads that provide background support and services to user threads. These can be garbage collection, finalizer thread, and thread scheduler. Daemon threads do not prevent the JVM from exiting when all user threads have finished execution. They can be created with the setDaemon(true) method on a Thread object before starting it.
3) System threads - created and ran by the JVM in the background of the application. They are responsible for tasks like memory management, system management, and signal handling. They are not directly controlled by the application code.

Q: What is the difference between the start() and run() methods of a thread?
A: If the run() method is invoked directly, the code will be executed within the current calling thread and a new one will not be created. The start() method creates a new thread and the JVM invokes the run() method within that new thread. The start() method can be called only once as otherwise an illegal state exception will be thrown. 

Q: What is thread priority?
A: Thread priority in Java is a mechanism that allows threads to be assigned a priority level, typically in the form of an integer value. This priority level affects the order in which threads are scheduled for execution by the Thread Scheduler, which is part of the JVM. Priority levels in Java range from 1-10, with the default being 5. However, it is important to note that the priority levels are more suggestions that strict rules, the behavior can be highly platform-dependent since the JVM delegates the thread scheduling to the underlying operating system.

Q: What is a stream in Java?
A: A stream represents a sequence of elements supporting sequential and parallel aggregate operations. It provides an interface for data processing queries such as filtering, sorting, mapping, and reduction, in a declarative way. Even though it is designed to work with collections, unlike them, streams do not store data.

Q: What are intermediate and terminal operations in streams?
A: Each step in the pipeline of a stream operation can be intermediate (returning a new stream) or terminal (producing a result or side-effect). Intermediate operations can be chained and none of them will take an effect until a terminal operation is invoked. Terminal operations cannot be chained. Another important difference is that intermediate operations are lazy in nature while the terminal ones are eager. 

Q: What is Optional in Java?
A: "Optional" is a container object which may or may not contain a non-null value. Its primary purpose is to provide a means to avoid "NullPointerException" which is often found in Java code, making the code more readable and errors less likely as the case where a value may be missing has to be actively thought of.

Q: How many ways are there to create a String object?
A: There are two ways - by using "" (string literal) or by using the new keyword new String ("String"). When a string is created with double quotes, the JVM searches for it in the string pool, and if the same value is found, a reference to that string is provided, else it creates a new object with the provided value. If using the "new" keyword, the JVM creates a new object but not in the string pool, if we want to create the object in the string pool the intern() method has to be used.

Q: Why does Java use a string pool?
A: The string pool provides the facility of reusability for existing string object. This helps in speeding up the application and saving memory as no two objects will have the same content.

Q: What does the String intern() method do?
A: Its' purpose is to put a String into the string constant pool. If the string already exists there, a reference to it is returned, if not, the string is put in the pool and then a reference to it is returned. 

Q: Why do we override hashcode() and equals() methods?
A: This is a crucial when creating classes which can be stored in hash-based collections like HashSet, HashMap, HashTable and LinkedHashSet or when instances are compared. The default equals method checks the object reference for equality, and in many cases, this behavior is not desired. The hash-based collections use hash codes to efficiently organize and retrieve objects. If two objects are considered equal according to equals() method, they must also have the same hash code. However, two objects with the same hash code are not necessarily equal. This ensures equal objects are stored in the same or nearby buckets in hash-based collections, which optimizes lookup, insertion, and deletion operations. 

Q: What is Metaspace and how does it differ from PermGen?
A: Metaspace and PermGen (Permanent Generation) are both memory spaces in the Java Virtual Machine (JVM) but pertain to different versions of the JVM. The key difference between them lies in how they store metadata information about the classes and methods in a Java application and how they are managed and optimized by the JVM. The transition from PermGen to Metaspace happened with the introduction of Java 8. PermGen was a fixed-size memory space separate from the Java heap where the JVM stored metadata about the classes and methods in the application, as well as other class-level information like static variables. Like PermGen, Metaspace stores metadata about the classes in the application. However, it does so with notable improvements in memory management, like dynamic resizing, improved garbage collection and configuration.

Q: What are generics in Java?
A: Generics are a feature of Java that allow classes, interfaces, and methods to operate on types (classes and interfaces) specified by the programmer at the time of use, making code reusable and more robust. Generics enhance the language by allowing types (classes and interfaces) to be parameters when defining classes, interfaces, and methods, much like the way arguments are passed into methods.

Q: What is the difference between == and equals()?
A: In Java, == and equals() are used for comparison, but they serve different purposes and work in distinct ways. The == operator is used for reference comparison in the case of objects, and for value comparison in the case of primitive types. The equals() method is intended for content comparison. It's used to determine if two objects are "equal" in terms of the data or information they hold. In the Object class, the equals() method is implemented to be equivalent to the == operator, meaning it checks for reference equality. However, it's meant to be overridden in subclasses to compare the state (content) of objects.

Q: What is autoboxing and unboxing?
A: Autoboxing is the automatic conversion that the Java compiler makes between the primitive types (int, double, etc.) and their corresponding object wrapper class (Integer, Double, etc.). If the conversion is from wrapper to primitive it is called unboxing.

Q: What are Varargs?
A: Varargs (Variable Arguments) is a feature introduced in Java 5 that allows a method to accept an arbitrary number of arguments of a specified type. To declare a method with varargs, you use the ellipsis (...) after the parameter type.

Q: What is the volatile keyword in Java?
A: The volatile keyword ensures that changes to a variable are immediately visible to all threads. It prevents threads from caching variables locally and ensures that the value is always read from and written to the main memory.

Q: What is the transient keyword in Java?
A: The transient keyword is used in serialization to indicate that a field should not be serialized. When an object is deserialized, transient fields are initialized to their default values.

