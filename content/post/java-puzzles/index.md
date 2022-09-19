---
title: 'Java Puzzles'
description: 'Java Puzzles'
summary: 'Few puzzles in java, to test the fundamentals'
date: '2022-02-18'
aliases: [/java-puzzles/]
author: 'Arjun Surendra'
categories: [Puzzles]
tags: [java-puzzles]
toc: true
---

Few puzzles in java, to test the fundamentals

Github: [https://github.com/gitorko/project01](https://github.com/gitorko/project01)

# Puzzles

## Puzzle: 1 (pass by value vs pass by ref)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_001_passbyvalue/PassByPuzzle.java" >}}

**Solution**

Java always does **pass by value**. In the case of reference they are still passed by value but since they point to same memory location they update the same object.

## Puzzle: 2 (finally block)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_002_exception/ExceptionPuzzle.java" >}}

**Solution**

The **finally** block can still return a value if exception is thrown.

## Puzzle: 3 (static variable vs instance variable)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_003_static/StaticPuzzle.java" >}}

**Solution**

The **static variables** are common to all instances, so instance variables should not be static.

## Puzzle: 4 (equals & hashcode)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_004_hashcode/ObjectPuzzle.java" >}}

**Solution**

How 2 object are same is determined only if you override the equals and hashcode method.
Set ensured that unique elements are stored but how does the set know that both these objects are same?
That's why you need to override **equals and hashcode**.
As a follow up you can read why hashcode and equals method need to be overridden together.
What happens if you dont do them together?

## Puzzle: 5 (Immutable class)

Is the class immutable?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_005_immutable/ImmutablePuzzle.java" >}}

**Solution**

Class is not **immutable**, have to clone both hashmap and date to avoid modification.
1. Make class final to avoid extending it.
2. Remove setter method
3. Make variables final so that they can be init only via constructor.
4. Defensive copy of any variables that return reference objects. Deep copy vs shallow copy difference is important here.
   Reflection can still break immutability.

## Puzzle: 6 (string pool vs heap)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_006_datatype/DataTypePuzzle.java" >}}

**Solution**

The **String pool** holds the strings, using new String() creates the string in heap.
The difference between == and .equals() where the first checks the address and the second checks the value.
To move an element from heap to string pool we use intern.
As a follow up to this, why we shouldn't store/use password as string in java instead we should store password as char array?
Because string pool objects will remain in memory longer than heap objects. if you create a password in string the string pool will not GC it after the function is done. So the password can remain for a long time.
If someone gets a heap dump they can look at the passwords. Hence better to store in char so that object is GC after it goes out of scope.

## Puzzle: 7 (memory leak)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_007_map/MapPuzzle.java" >}}

**Solution**

Java has automatic memory management in terms of garbage collection unlike c/c++, hence ideally it means that there should be no memory leak, logic being garbage collector always identifies the objects that are not used and garbage collected.
The puzzle above is a clear example of a **memory leak** in Java.
The key of a Map has to be immutable, this is one of the hard requirements for a map. If you see someone using an object as key without equals/hashcode you raise a red flag. If the object is not immutable and used as a key in map you raise a red flag.

Hashmap is a array with each node of array pointing to a linkedlist.
Jack was put on the hashmap table, but the value was changed to Rose.
Rose hashcode will never point to the bucket of Jack.
Jack hashcode will point to the bucket where Rose is present but then when it reaches that node it will again check if objects are same, in this case its not, so it will return null.
So you will never be able to get jack. This is an example of memory leak in java. If it happens for many elements then jvm will crash. Garbage collector cant clean it because its still part of the map, but no one can reach it.

## Puzzle: 8 (ThreadLocal)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_008_threadlocal/ThreadPuzzle.java" >}}

**Solution**

The program will work sometimes and will fail sometimes.
Reason is SimpleDateFormat is not thread safe. The same object is sent to all threads and even though it looks like they are using SimpleDateFormat to just parse, internally SimpleDateFormat does few operation that are not thread safe.
So now you might think i will pass a new SimpleDateFormat to each thread. However this is a costly object that will increase memory.
This is where ThreadLocal comes into picture, Threadlocal will keep a copy of the object specific to that thread.

What is the difference between copy of SimpleDateFormat vs using new SimpleDateFormat() each time?
Copy objects will be == number of threads
new objects will be > number of threads.
So if you have 10K dates, you will end up creating 10k new objects if you do new()
With ThreadLocal you will at max have 5 SimpleDateFormat objects if the thread pool is of size 5.

This is the advantage of using **ThreadLocal**.

## Puzzle: 9 (AtomicLong vs LongAdder)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_009_counter/CounterPuzzle.java" >}}

**Solution**

There are 2 problem in the code above.
Each thread is updating counter without syncronization block/lock, so what value the thread read and what value it wrote back is not guaranteed.
Your first thought might be to make the variable volatile so that copy of counter is kept in main memory and not thread memory.
However if every thread is reading and writing at different times even volatile wont help.
So you might think of putting a syncronization block but that will impact performance.
So using a AtomicLong is a good approach here. It guarantees that the CAS (Compare and Swap) operation is atomic and hence counter will be correct.
There is another hidden problem that is long + 1 is not a single operation. integer + 1 is a single operation, but long + 1 is not a single operation even within jvm. So this can lead to race condition. Long + 1 takes 2 operation in JVM to add.
The **AtomicLong** solves the problem but again is not optimal as it can lead to contention when there are lot of requests. This is when you use LongAdder which also guarantees count is 250 but the way it does it is different.
**LongAdder** maintains an array of counters, each thread updates a different element in the array with the count +1 and when you finally call sum, it adds the array. This means there is no contention because each thread is writing to a different block.

## Puzzle: 10 (volatile vs AtomicBoolean)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_010_volatile/VolatilePuzzle.java" >}}

**Solution**

The puzzle explains the concept of thread memory cache.
When you call a thread it creates a thread stack that maintains a copy of the global variable. If the variable changes within the thread the changes are flushed, however if the variable changes outside the changes will not be synchronised immediately.
Thread can continue to use the local copy of the variable not knowing that it has already changed.
In the puzzle above even though we are change the boolean, the thread doesnt know the boolean changed in the main thread so it continues to refer to its local copy in cache.
How to prevent the thread from maintaining a local copy of the variable cache and always refer to global variable?
You can use **volatile** or AtomicBoolean

## Puzzle: 11 (instance lock vs class lock)

What is the output of the program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_011_instanceclasslock/InstanceClassLockPuzzle.java" >}}

**Solution**

There are 2 types of locks **instance lock** and **class lock**.

## Puzzle: 13 (Double check locking)

What is the problem with this program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_013_doublechecklock/CheckLockingPuzzle.java" >}}

**Solution**

Introduces the concept of **double check locking**, where each thread accessing the syncronized block is a costly operation, hence doing a null check before and after the syncronization improves performance.

## Puzzle: 14 (Race Condition)

What is the problem with this program?

{{< ghcode "https://raw.githubusercontent.com/gitorko/project01/main//src/test/java/com/demo/basics/puzzle/_014_racecondition/RacePuzzle.java" >}}

**Solution**

**Race condition**, two threads can try to update at the same time leading to data corruption. Using the atomic putIfAbsent should fix it.
