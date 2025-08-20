## Sleep:
1. why sleep is a static method?
   ```java
   Thread.sleep(1000);
   ```
   `sleep` always affects the currently executing thread, not some arbitrary thread object.  
   If it were an instance method (like `t.sleep(1000)`), people might think it pauses the thread t. which is misleading.
2. **DOES NOT RELEASE LOCK.**

## wait():
1. why wait is called on object?
   `wait()` is tied to an object’s monitor (lock)
   ```java
   synchronized (sharedObject) {
      while (!condition) {
          sharedObject.wait();   // releases lock on sharedObject
      }
    // continues when notified
    }
    ```
   > it says: "I’m waiting on this object’s monitor until someone notifies me."
 2. **ONLY FUNCTION THAT RELEASE LOCK.**

## Notify():
 1. wakes up one thread thats waiting for object lock.
 2. which thread is woken (if multiple are waiting) is up to the JVM scheduler.
 3. notify(): If only one thread needs to proceed (e.g., producer-consumer, one consumer per produced item). Like BlockingQueue

## NotifyAll():
1. wakes up all the threads that are waiting on he object.
2. e.g., many consumers waiting for different conditions.

## Join():
1. `join()` is a metho d of the Thread class that makes the current thread wait until another thread has finished execution.
2. **DOES NOT RELEASE LOCK.**
   ```java
    Thread worker = new Thread(() -> {
            try {
                Thread.sleep(2000); // simulate work
                System.out.println("Worker finished");
            } catch (InterruptedException e) {}
        });

    worker.start();
    
    System.out.println("Main waiting for worker...");
    worker.join(); // main thread waits here
   ```
## Thread Lifecycle:

```
 ┌──────────┐        start()        ┌──────────────┐
 │   NEW    │  ------------------>  │   RUNNABLE   │ // Thread is eligible to run, waiting for CPU scheduling
 └──────────┘                       └──────┬───────┘
  //Thread object created,                 │
//but not yet scheduled or started         │
                                           │ CPU picks
                                           ▼
                                      ┌───────────┐
                                      │  RUNNING  │
                                      └─────┬─────┘
                                            │
          run() ends                        │
                ┌───────────────────────────┘
                ▼
          ┌────────────┐
          │ TERMINATED │
          └────────────┘

From RUNNING:  
   │
   ├──> wait()        ───> WAITING
   │
   ├──> sleep(ms) / 
   │     join(ms) /
   │     wait(ms) ───> TIMED_WAITING
   │
   └──> try to get lock
         (another thread owns it) ───> BLOCKED

WAITING / TIMED_WAITING ──notify/notifyAll/timeout──> RUNNABLE  
BLOCKED ──lock available──> RUNNABLE
```
