lets say we have a system that accepts only 3 threads to a function. If there are more threads they all will enter the code, whereas locking/synchronizing a function would allow only one thread. To fix this we use Semaphores.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/714b8865-1aeb-4038-9f6d-9647b227e1a5/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/714b8865-1aeb-4038-9f6d-9647b227e1a5/Untitled.png)

```java
public static void main(String[] args){
		ExecutorService service = Executors.newFixedSizePool(nThreads:50);
		IntStream.of(1000).forEach(i -> service.execute(new Task()));
		// this would call the function 1000 times shared by 50 threads.
		service.shutdown();
		service.awaitTermination(timeout: 1, TimeUnit.Minutes);
	}

static class Task implements Runnable{
		
		@Override
		public void run(){
			//some processing...
			// IO call to slow service.   --->  //if not used semaphore this would be called 
																					//50 time concurrently.
			// rest of Processing.
			}
}
```

region change, Kms id chage, pool capacity.

For this we use Semaphors. Each green stripes define a permit.

A thread comes if available acquires a permit. And moves forward.

upto 3 threads in this case can acquire the permit.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c7750572-3862-4c68-a04d-fb66d58ae048/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c7750572-3862-4c68-a04d-fb66d58ae048/Untitled.png)

if thread 4 tries to acquire the lock/permit it fails and is blocked until someone releases the permit.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/948c37c8-a680-4cf5-91c3-61b807845854/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/948c37c8-a680-4cf5-91c3-61b807845854/Untitled.png)

if there are more threads they also get blocked and only active if there are permits available

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/243c69f9-44fa-4310-adb2-8c895d0332ea/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/243c69f9-44fa-4310-adb2-8c895d0332ea/Untitled.png)

```java
public static void main(String[] args){
		ExecutorService service = Executors.newFixedSizePool(nThreads:50);

		Semaphore semaphor = new Semaphore(permit:3);   <----------------this is the change
		IntStream.of(1000).forEach(i -> service.execute(new Task(semaphore)));  <---- this is the change
		// this would call the function 1000 times shared by 50 threads.
		service.shutdown();
		service.awaitTermination(timeout: 1, TimeUnit.Minutes);
	}

static class Task implements Runnable{
		
		@Override
		public void run(){
			//some processing...
			semaphore.acqiure();     ---> //we can either use acquireUninterruptably() to avoid throwing
																		// Interrupted exception by acquire() method.
			// IO call to slow service.   --->  //if not used semaphore this would be called 
																					//50 time concurrently.
			semaphore.release();
			// rest of Processing.
			}
}
```
