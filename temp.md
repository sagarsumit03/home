# Java & Architecture Notes

---

## 🧠 Class Loading

1. Reference the class name in code; JVM loads it when first referenced:

   ```java
   SomeClass someInstance = null;
   ```
2. Use `Class.forName("XYZ")` to load and **initialize** the class via current classloader.
3. Use `ClassLoader.getSystemClassLoader().loadClass("XYZ")` to **load without initializing** (static blocks not executed yet).

---

## 🧶 Daemon Threads

* Daemon threads run in background (like GC), scheduled only when CPU is idle, and die when all user threads complete.
* Always have priority `MIN_PRIORITY (1)`.
* Check with `isDaemon()`; create by `setDaemon(true)` **before** `start()`. Changing afterward throws `IllegalThreadStateException`.

---

## 🔁 wait(), notify(), notifyAll()

* `wait()` — current thread waits and releases lock until `notify()` or `notifyAll()` is called.
* `notify()` — wakes one waiting thread (chosen arbitrarily).
* `notifyAll()` — wakes all waiting threads; they compete for lock.

---

## 📆 Callable vs Future vs CompletableFuture

| Feature            | `Future`             | `CompletableFuture`                      |
| ------------------ | -------------------- | ---------------------------------------- |
| Non-blocking       | ❌ Blocks on `.get()` | ✅ Supports callbacks (`thenApply`, etc.) |
| Chaining           | ❌ Not supported      | ✅ Easily chainable                       |
| Exception handling | ❌ Manual on `.get()` | ✅ Built-in (`.exceptionally()`)          |

* `Callable` is like `Runnable` but **returns a result** and can throw exceptions.
* `Future` represents async result and supports checking/completion.

---

## 🏗️ Architecture Styles: SOA vs Microservices

* **SOA**: Share-as-much-as-possible; uses SOAP/XML.
* **Microservices**: Share-as-little-as-possible; uses REST/HTTP or AMQP.
* Both emphasize service reuse, but microservices are more **loosely coupled**.

---

## 📘 Domain-Driven Design (DDD)

* Model software based on real-world **domain experts**.
* Divide application into **bounded contexts** (e.g., Ordering, Shipping, Payments).
* Each domain context is autonomous but integrates with others.

---

## 💡 Using `Optional`

* Primary goal: represent **absent values** gracefully instead of nulls.

---

## 🔧 Spring Dependency Injection (DI)

* If multiple beans implement same interface, use:

  * `@Qualifier("beanName")` or
  * `@Primary` to indicate default bean.

* `@Qualifier` takes precedence over `@Primary`.

* Example:

  ```java
  @Component("goat")
  public class FetchFromDB implements Fetch { … }

  @Autowired
  @Qualifier("goat")
  Fetch fetch;
  ```

* Constructor injection example:

  ```java
  @Autowired(required = false)
  public BooksController(FetchFromDB db, FetchFromMongo mongo) { … }
  ```

* **Note:** All used classes must be annotated as Spring beans (e.g., `@Component`, `@Service`), otherwise app startup fails.

---
