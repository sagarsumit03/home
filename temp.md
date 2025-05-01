## ways to load a class:
1.  Reference the class name in the code. The class will be loaded latest when the JVM finds that reference.
    
    ```java
    SomeClass someInstance = null;
    ```
    
2.  [Class.forName(String)](http://docs.oracle.com/javase/6/docs/api/java/lang/Class.html#forName%28java.lang.String%29), to load and initialize the class. It uses classloader of current class.
    
    ```java
     Class.forName("XYZ");
    ```
    
3.  [ClassLoader#loadClass(String)](http://docs.oracle.com/javase/6/docs/api/java/lang/ClassLoader.html#loadClass%28java.lang.String%29), to load class, but doesn't initialize.You can get an instance of  `ClassLoader`  and invoke  `loadClass()`  on that instance, which can be a Custom ClassLoader or System ClassLoader.
    
    ```java
    ClassLoader.getSystemClassLoader().loadClass("XYZ");
    ```
    “does not initialize” means: the class is known to the JVM, but its **static code is not yet run**.

---
1.  Thread scheduler schedules these threads only when CPU is idle.
    
2.  Daemon threads are service oriented threads, they serves all other threads.
    
3.  These threads are created before user threads are created and die after all other user threads dies.

4.  Priority of daemon threads is always 1 (i.e. MIN_PRIORITY).
    
5.  User created threads are non daemon threads.
    
6.  JVM can exit even if Daemon thead is running.
    
7.  Daemon threads are low priority threads which runs intermittently in background for doing garbage collection.
    
8.  we can use isDaemon() method to check whether thread is daemon thread or not.

9.  we can use setDaemon(boolean on) method to make any user method a daemon thread. Generally, We must not make user threads as daemon.
   
10.  If setDaemon(boolean on) is called on thread after calling start() method than IllegalThreadStateException is thrown.
---
## Callable and Future:

-   The Callable interface is similar to Runnable but can return a result and throw checked exceptions.
-   Future represents the result of an asynchronous computation and provides methods for checking if the computation is complete and retrieving the result.

---
1.  `**wait**()`

Purpose: Causes the current thread to wait until another thread invokes  `notify()`  or  `notifyAll()`  on the same object.

Behaviour: Releases the lock and enters the waiting state.

2.  `**notify**()`

Purpose: Wakes up one waiting thread.

Behaviour: Only one thread is notified and attempts to acquire the lock. The choice is random.

3.  `**notifyAll**()`

Purpose: Wakes up all waiting threads.

Behaviour: All threads are notified but only one can acquire the lock at a time.

## CompletableFuture vs Future

Non-blocking result

❌ Must call `.get()` (blocking)

✅ Can be non-blocking with callbacks like `.thenApply()`

Chaining tasks

❌ Not supported

✅ Easily chain tasks (`thenApply`, `thenCompose`, etc.)
Exception handling

❌ Manual try-catch around `.get()`

✅ Built-in methods like `.exceptionally()`


---
**SOA:**

-   Follows “share-as-much-as-possible” architecture approach
-   Uses SOAP and XMLs
-   Maximizes application service reusability

**MicroService:**

-   Follows “share-as-little-as-possible” architecture approach
-   Uses lightweight protocols such as HTTP/REST & AMQP
-   More focused on decoupling
**Domain Driven Architecture**:
* DDD is about trying to make your software a model of a real-world system or process. In using DDD, you are meant to work closely with a _[domain expert](http://en.wikipedia.org/wiki/Domain_expert)_ who can explain how the real-world system works.
* Application is Divided into different domains:
* In an online store, you'd have:

-   🛒 **Ordering** – deals with shopping carts and purchases.
    
-   📦 **Shipping** – deals with packaging and delivery.
    
-   💰 **Payments** – handles money.
    

Each one works separately but talks to the others.


---
The main design goal of `Optional` is to provide a means for a function returning a value to indicate the absence of a return value.
