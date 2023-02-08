lets take an example where we have to book movie seats and there are currently 4 threads trying to book the ticket. without a locking mechanism all the threads would try to book the seats and if they book same seats it would be a problem.

So to avoid this we introduced locks. Lock ensure that only the owner of the lock ( any thread who acquires the lock first) would be able to book the seats first. Once the thread is done, it would release the lock.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c710a918-0fd3-4c15-94a1-a4f8eec5e1ab/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c710a918-0fd3-4c15-94a1-a4f8eec5e1ab/Untitled.png)

This is how we define a reentrant lock:

```java
private ReentrantLock lock = new ReentrantLock();
public void run() {
    lock.lock();
    try {
    boolean isBooked = show.bookSeats(this.userName, this.userId, bookingSeats);
    }finally {
    lock.unlock();
    }
}
```

We could also use synchronized keyword for locking.

```java
public synchronized boolean bookSeats{ -----> similar to lock.lock()
...
}                                      ------> similar to lock.unlock()
```

The difference is lock is explicit because we define a variable for it whereas synchronized block or function is implicit.

Another difference is lock has methods to try locking `tryLock(timeout)`

---

Why this is called a Reentrant lock?

lets say we have the below code now, as we see on `line number: 6`, the code is trying to call itself recursively. Now again it will try to acquire the lock as per `line number:3` , but the thread already has a lock. This is why its called reentrant lock. only thing is its `getHoldCount()` would increase. 

Note: `getHoldCount`tells how many times a thread has acquired a lock.

```java
1. private static ReentrantLock lock - new ReentrantLock();
2. private static void accessResource(){
3. 	lock.lock();
4. 	... do some work...
5. 	if(some condition()){
6. 		accessResource();
7. 	}
8. 	lock.unlock();
9. }
```

There is another constructor for Reentrant Lock = `new ReentrantLock(fair).`

This allows to give all threads chance to acquire lock and avoid thread starvation.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c33ae628-a52c-4f65-8a8a-8fcf27ece8c1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c33ae628-a52c-4f65-8a8a-8fcf27ece8c1/Untitled.png)

And lastly there is `tryLock()` function which tries to acquire a lock. It also accepts a timeout.

```java
1. private static ReentrantLock lock = new ReentrantLock();
2. private static void accessResource(){
3. 	  boolean isLockAcquired = lock.tryLock(timeout: 5,TimeUnit.Seconds);
4. 	  ... do some work...
5. 	  if(isLockAcquired){
6. 		    accessResource();
7.        lock.unlock();
8. 	   }else{
9.        .... do something else...	
10.    }
11. }
```

---

# ReadWriteLock.

its similar to the `reentrantLock` but allows multiple threads to read while only 1 thread to write. `But it doesn't allow parellel reading and writing. if there are 100 threads and 50 are reading, once all 50 are done reading only the writing threads can write something.`

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/daf016ee-3e88-4719-9c02-f02a024fae4c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/daf016ee-3e88-4719-9c02-f02a024fae4c/Untitled.png)

below is the sample code:

```java
private static ReentrantLock lock - new ReentrantLock();
private ReentrantReadWriteLock.ReadLock readLock = lock.readLock();
private ReentrantReadWriteLock.WriteLock writeLock = lock.writeLock();
private static void readResource(){
   readLock.lock();
 	 ... do some work...
   readLock.unlock();
 }

 private static void writeResource(){
   writeLock.lock();
 	 ... do some work...
   writeLock.unlock();
 }

public static void main(String args[]){
    new Thread(() -> readResource()).start();
    new Thread(() -> readResource()).start();
    new Thread(() -> writeResource()).start();
    new Thread(() -> writeResource()).start();
```
