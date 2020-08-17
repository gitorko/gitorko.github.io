---
title: Producer Consumer
date: 2019-09-15 00:00:00
tags:
- design pattern
categories:
- [Design Pattern]
---

Producer consumer problem implementation using different approaches

<!-- more -->

<!-- toc -->

## Producer Consumer

Producer consumer using ArrayBlockingQueue.

ProduceConsumer.java

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

public class ProduceConsumer {

    public static void main(String[] args) {
        // BlockingQueue<String> queue = new SynchronousQueue<>();
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(10);

        final Runnable producer = () -> {
            for (int i = 0; i < 20; i++) {
                try {
                    queue.put(String.valueOf(i));
                    System.out.println("Published: " + i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        final Runnable consumer = () -> {
            while (true) {
                try {
                    System.out.println("Consumed" + queue.take());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        new Thread(producer).start();
        new Thread(consumer).start();
        System.out.println("Producer and Consumer has been started");
    }
}
```

Producer consumer using wait notify.

PCWaitNotify.java

```java
import java.util.LinkedList;
import java.util.Queue;

public class PCWaitNotify {

    public static void main(String[] args) {
        MyBlockingQueue2<String> queue = new MyBlockingQueue2<>();
        final Runnable producer = () -> {
            for (int i = 0; i < 20; i++) {
                queue.put(String.valueOf(i));
                System.out.println("Published: " + i);
            }
        };

        final Runnable consumer = () -> {
            while (true) {
                System.out.println("Consumed: " + queue.take());
            }
        };

        new Thread(producer).start();
        new Thread(consumer).start();
    }
}

class MyBlockingQueue2<E> {
    private Queue<E> queue = new LinkedList<>();
    private int max = 10;

    public void put(E e) {
        synchronized(queue) {
            try {
                if(queue.size() == max){
                    queue.wait();
                }
                queue.add(e);
                queue.notifyAll();;
            } catch (InterruptedException ex) {
                ex.printStackTrace();
            }
        }
    }

    public E take() {
        synchronized(queue) {
            try {
                while(queue.size() == 0) {
                    queue.wait();
                }
                E item = queue.remove();
                queue.notifyAll();
                return item;
            } catch (InterruptedException e) {
                e.printStackTrace();
                return null;
            }
        }
    }
}
```

Producer Consumer using locks

PCLock.java

```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class PCLock {
    public static void main(String[] args) {

        MyBlockingQueue<String> queue = new MyBlockingQueue<>();
        ExecutorService executor = Executors.newFixedThreadPool(10);

        final Runnable producer = () -> {
            for (int i = 0; i < 20; i++) {
                queue.put(String.valueOf(i));
                System.out.println("Published: " + i);
            }
        };

        final Runnable consumer = () -> {
            while (true) {
                System.out.println("Consumed: " + queue.take());
            }
        };

        executor.submit(producer);
        executor.submit(consumer);
    }
}

class MyBlockingQueue<E> {
    private Queue<E> queue = new LinkedList<>();
    private int max = 10;
    private Lock lock = new ReentrantLock(true);
    private Condition notFull = lock.newCondition();
    private Condition notEmpty = lock.newCondition();

    public void put(E e) {
        lock.lock();
        try {
            if (queue.size() == max) {
                notFull.await();
            }
            queue.add(e);
            notEmpty.signalAll();
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public E take() {
        lock.lock();
        try {
            while (queue.size() == 0) {
                notEmpty.await();
            }
            E item = queue.remove();
            notFull.signalAll();
            return item;
        } catch (InterruptedException e) {
            e.printStackTrace();
            return null;
        } finally {
            lock.unlock();
        }
    }

}
```