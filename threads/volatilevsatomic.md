# Volatile Vs Atomic

## Visibility Problem

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/85790ebe-e9a5-4b76-9bf3-13e1ad8b09e1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/85790ebe-e9a5-4b76-9bf3-13e1ad8b09e1/Untitled.png)

lets say there are 2 threads thread1 and thread2. Now they both are working on the `boolean flag` 

thread2 has a condition that while the flag is true do something,

Similarly thread1 is making the flag as false. Which if later read by thread2 would cause thread2 to exit the condition.

The problem with above approach is that it wouldn't work. because the thread1 is making flag as false, which is cached in the local Cache of the core and not the shared Cache. Hence thread1 never knows if the thread1 has already made it false or not.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/235fd5a3-a4df-426a-8ac1-4c71979302b1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/235fd5a3-a4df-426a-8ac1-4c71979302b1/Untitled.png)

to fix this we use the keyword `volatile.` this will assure that any changes to the variable flag by any thread would be pushed down to the shared cache and would be refreshed in the other core's local cache.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eb70b84b-d135-4f4c-a22f-a2bbea9ea11e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eb70b84b-d135-4f4c-a22f-a2bbea9ea11e/Untitled.png)

---

## Synchronization Problem

lets say we have a variable `value =1`.

two threads thread1 and thread2 are parallelly trying to increment the `value`. 

The problem here is that incrementing a value is not an atomic process, rather 3 steps: 

1. read the value.
2. modify it
3. write the value. 

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5fdcd111-e2b7-44fc-9c10-45971e76db6b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5fdcd111-e2b7-44fc-9c10-45971e76db6b/Untitled.png)

There can be a scenario, where thread1 has read the `value =1` but before modifying it, thread2 also reads `value=1`. now when thread1 increments it `value` becomes 2. Same for thread2.

but this should not happen, if thread1 first read the `value`  it should get the `value =2` and thread2 should get `value = 3` .

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/468f9656-f1f9-43d0-b2bb-f7137854a808/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/468f9656-f1f9-43d0-b2bb-f7137854a808/Untitled.png)

to fix this we can use `synchronized` to make sure only 1 thread is acting on the increment of `value`

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/572c8ed9-5741-4eb9-ba50-16578c42d832/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/572c8ed9-5741-4eb9-ba50-16578c42d832/Untitled.png)

Another way to fix this is use the keyword `atomic` which has property `atomicInteger.incrementAndGet()` or `atomicInteger.decrementAndGet()` 

[Summary ](https://www.notion.so/e1eef033437e470cbd5fe4eb69a515b6)
