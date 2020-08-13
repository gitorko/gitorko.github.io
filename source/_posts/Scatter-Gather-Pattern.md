---
title: Scatter Gather Pattern
date: 2020-08-11 00:00:00
tags:
---

Scatter Gather enterprise integration pattern is used for scenarios such as “best quote”, where we need to request information from several suppliers and decide which one provides us with the best price for the requested item.

<!-- more -->

<!-- toc -->

## Scatter Gather Pattern

So we have a book product and we need to fetch the price from various sources and at max we can wait for 3 seconds.

## Code

Application.java

```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import lombok.AllArgsConstructor;
import lombok.SneakyThrows;

public class Application {

    ExecutorService threadPool = Executors.newCachedThreadPool();

    public static void main(String[] args) {
        Map<String, Float> book1Prices = new Application().getPrices1("book1");
        System.out.println(book1Prices);
    }

    @SneakyThrows
    private Map<String, Float> getPrices1(String productId) {
        Map<String, Float> prices = new ConcurrentHashMap<>();
        CountDownLatch latch = new CountDownLatch(3);
        threadPool.submit(new FetchTask("http://amazon", productId, prices, latch));
        threadPool.submit(new FetchTask("http://ebay", productId, prices, latch));
        threadPool.submit(new FetchTask("http://flipkart", productId, prices, latch));
        latch.await(3, TimeUnit.SECONDS);
        return prices;
    }

}

@AllArgsConstructor
class FetchTask implements Runnable {

    String url;
    String productId;
    Map<String, Float> prices;
    CountDownLatch latch;

    @SneakyThrows
    @Override
    public void run() {
        if (url.contains("amazon")) {
            //http fetch from amazon
            TimeUnit.SECONDS.sleep(2);
            prices.put("amazon", 2.35f);
        }

        if (url.contains("ebay")) {
            //http fetch from ebay
            TimeUnit.SECONDS.sleep(4);
            prices.put("ebay", 2.30f);
        }

        if (url.contains("flipkart")) {
            //http fetch from flipkar
            TimeUnit.SECONDS.sleep(1);
            prices.put("flipkart", 2.10f);
        }
    }

}
```

Run the project, You will see the quotes that were fetched within 3 seconds.

```
{amazon=2.35, flipkart=2.1}
```