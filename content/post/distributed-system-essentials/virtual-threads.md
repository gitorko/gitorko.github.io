Virtual threads aim to improve the concurrency model in Java by introducing lightweight, user-mode threads that can efficiently handle a large number of concurrent tasks.

If your code calls a blocking I/O operation in a virtual thread, the runtime suspends the virtual thread until it can be resumed later.
The hardware is utilized to an almost optimal level, resulting in high levels of concurrency and, therefore, high throughput.

**Pitfalls to avoid in Virtual Threads**

1. Exceptions - Stack traces are separate, and any Exception thrown in a virtual thread only includes its own stack frames.
2. Thread-local - Reduce usage as each thread will end up creating its own thread local unlike before where there are limited threads in pool, virtual threads can be many as they are cheap to create.
3. Synchronized blocks/methods - When there is synchronized method or block used the virtual thread is pinned to a platform thread, it will not relinquish its control. This means it will hold the platform thread which can cause performance issues if there is IO happening inside the synchronized block. Use ReentrantLock instead of synchronized.
4. Native code - When native code is used virtual threads get **pinned** to platform threads, it will not relinquish its control. This may be problematic if IO happens for longer time there by blocking/holding the platform thread.
5. Thread pools - Avoid thread pool to limit resource access, eg: A thread pool of size 10 can create more than 10 concurrent threads due to virtual threads hence use semaphore if you want to limit concurrent requests based on pool size.
6. Spring - In sprint context use `concurrency-limit` to limit number of thread pool and avoid runway of virtual threads.
7. Performance - Platform threads are better when CPU intensive tasks are executed compared to virtual threads. Virtual threads benefit only when there is IO.
8. Context switching - When virtual threads have blocking operation they yield and JVM moves the stack to heap memory. The stack is put back only when its time to execute the thread again. This is still cheaper than creating a new platform thread though.

```bash
Runnable fn = () -> {
  // your code
};

Thread thread = new Thread(fn).start();

Thread thread = Thread.ofPlatform().start(runnable);
                      
Thread thread = Thread.ofVirtual(fn).start();

Thread.startVirtualThread(fn);

var executors = Executors.newVirtualThreadPerTaskExecutor();
executors.submit(() -> {
  // your code
});
```

![](virtual-threads-jvm.png)