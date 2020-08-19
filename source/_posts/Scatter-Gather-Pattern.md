---
title: Scatter Gather Pattern
date: 2020-08-11 00:00:00
tags:
- scatter gather
categories:
- [Design Pattern]
---

Scatter Gather enterprise integration pattern is used for scenarios such as “best quote”, where we need to request information from several suppliers and decide which one provides us with the best price for the requested item.

<!-- more -->

<!-- toc -->

## Scatter Gather Pattern

So we have a book product and we need to fetch the price from various sources and at max we can wait for 3 seconds. You could use a Thread.sleep or Threads join() method but then if the tasks complete before 3 seconds the tasks will still wait for 3 seconds before returning.

## Code

We can use a CountDownLatch to wait for the prices to be fetched. It will wait only for 3 seconds and return the prices fetched.

ScatterGatherLatch.java

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import lombok.AllArgsConstructor;
import lombok.SneakyThrows;

public class ScatterGatherLatch {

    ExecutorService threadPool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        Map<String, Float> book1Prices = new ScatterGatherLatch().getPrices("book1");
        System.out.println(book1Prices);
    }

    @SneakyThrows
    private Map<String, Float> getPrices(String productId) {
        Map<String, Float> prices = new ConcurrentHashMap<>();
        CountDownLatch latch = new CountDownLatch(3);
        threadPool.submit(new FetchData("http://amazon", productId, prices, latch));
        threadPool.submit(new FetchData("http://ebay", productId, prices, latch));
        threadPool.submit(new FetchData("http://flipkart", productId, prices, latch));
        latch.await(3, TimeUnit.SECONDS);
        threadPool.shutdown();
        return prices;
    }

    @AllArgsConstructor
    class FetchData implements Runnable {

        String url;
        String productId;
        Map<String, Float> prices;
        CountDownLatch latch;

        @SneakyThrows
        @Override
        public void run() {
            if (url.contains("amazon")) {
                //http fetch from amazon
                System.out.println("Fetching price from amazon!");
                TimeUnit.SECONDS.sleep(2);
                prices.put("amazon", 2.35f);
                latch.countDown();
            }

            if (url.contains("ebay")) {
                System.out.println("Fetching price from ebay!");
                //http fetch from ebay
                TimeUnit.SECONDS.sleep(4);
                prices.put("ebay", 2.30f);
                latch.countDown();
            }

            if (url.contains("flipkart")) {
                System.out.println("Fetching price from flipkart!");
                //http fetch from flipkart
                TimeUnit.SECONDS.sleep(1);
                prices.put("flipkart", 2.10f);
                latch.countDown();
            }
        }
    }
}
```

We can also use the invokeAll method

ScatterGatherInvoke.java

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.concurrent.Callable;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import lombok.AllArgsConstructor;
import lombok.SneakyThrows;

public class ScatterGatherInvoke {
    ExecutorService threadPool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        Map<String, Float> book1Prices = new ScatterGatherInvoke().getPrices("book1");
        System.out.println(book1Prices);
    }

    @SneakyThrows
    private Map<String, Float> getPrices(String productId) {
        Map<String, Float> prices = new ConcurrentHashMap<>();
        List<Callable<Void>> tasks = new ArrayList<>();

        tasks.add(new FetchData("http://amazon", productId, prices));
        tasks.add(new FetchData("http://ebay", productId, prices));
        tasks.add(new FetchData("http://flipkart", productId, prices));
        threadPool.invokeAll(tasks, 3, TimeUnit.SECONDS);
        threadPool.shutdown();
        return prices;
    }

    @AllArgsConstructor
    class FetchData implements Callable<Void> {

        String url;
        String productId;
        Map<String, Float> prices;

        @Override
        @SneakyThrows
        public Void call() throws Exception {
            if (url.contains("amazon")) {
                //http fetch from amazon
                System.out.println("Fetching price from amazon!");
                TimeUnit.SECONDS.sleep(2);
                prices.put("amazon", 2.35f);
            }

            if (url.contains("ebay")) {
                System.out.println("Fetching price from ebay!");
                //http fetch from ebay
                TimeUnit.SECONDS.sleep(4);
                prices.put("ebay", 2.30f);
            }

            if (url.contains("flipkart")) {
                System.out.println("Fetching price from flipkart!");
                //http fetch from flipkart
                TimeUnit.SECONDS.sleep(1);
                prices.put("flipkart", 2.10f);
            }
            return null;
        }
    }
}
```

We can also use the CompletableFuture.

```java
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.TimeoutException;

import lombok.AllArgsConstructor;
import lombok.SneakyThrows;

public class ScatterGatherCompletable {
    ExecutorService threadPool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        Map<String, Float> book1Prices = new ScatterGatherCompletable().getPrices("book1");
        System.out.println(book1Prices);
    }

    @SneakyThrows
    private Map<String, Float> getPrices(String productId) {
        Map<String, Float> prices = new ConcurrentHashMap<>();

        CompletableFuture<Void> task1 = CompletableFuture.runAsync(new FetchData("http://amazon", productId, prices));
        CompletableFuture<Void> task2 = CompletableFuture.runAsync(new FetchData("http://ebay", productId, prices));
        CompletableFuture<Void> task3 = CompletableFuture.runAsync(new FetchData("http://flipkart", productId, prices));

        CompletableFuture<Void> allTasks = CompletableFuture.allOf(task1,task2,task3);
        try {
            allTasks.get(3, TimeUnit.SECONDS);
        } catch (TimeoutException ex) {
            //Do Nothing!
        }
        return prices;
    }

    @AllArgsConstructor
    class FetchData implements Runnable {

        String url;
        String productId;
        Map<String, Float> prices;

        @Override
        @SneakyThrows
        public void run() {
            if (url.contains("amazon")) {
                //http fetch from amazon
                System.out.println("Fetching price from amazon!");
                TimeUnit.SECONDS.sleep(2);
                prices.put("amazon", 2.35f);
            }

            if (url.contains("ebay")) {
                System.out.println("Fetching price from ebay!");
                //http fetch from ebay
                TimeUnit.SECONDS.sleep(4);
                prices.put("ebay", 2.30f);
            }

            if (url.contains("flipkart")) {
                System.out.println("Fetching price from flipkart!");
                //http fetch from flipkart
                TimeUnit.SECONDS.sleep(1);
                prices.put("flipkart", 2.10f);
            }
        }
    }
}
```

Result

```
{amazon=2.35, flipkart=2.1}
```