---
title: Java异步调用的几种方式
author: jason.xiang
date: 2022-09-02 13:30:00
tags:
 -  java
 -  多线程
categories: []
---

## 一、通过创建新线程

```java
public static void main(String[] args) throws Exception{

    System.out.println("主线程 =====> 开始 =====> " + System.currentTimeMillis());

    new Thread(() -> {
        System.out.println("异步线程 =====> 开始 =====> " + System.currentTimeMillis());
        try{
            Thread.sleep(5000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("异步线程 =====> 结束 =====> " + System.currentTimeMillis());
    }).start();

    Thread.sleep(2000);

    System.out.println("主线程 =====> 结束 =====> " + System.currentTimeMillis());
    
}

主线程 =====> 开始 =====> 1627893837146
异步线程 =====> 开始 =====> 1627893837200
主线程 =====> 结束 =====> 1627893839205
异步线程 =====> 结束 =====> 1627893842212

```

我们通过线程休眠来达成主线程执行时间2秒左右，异步线程执行5秒左右的效果。通过打印出来的时间戳倒数第四位（秒位）我们可以看出，两个的线程执行总时间为5秒左右，符合异步执行的特征

## 二、通过线程池

因为异步任务的实现本质的由新线程来执行任务，所以通过线程池的也可以实现异步执行。写法同我们利用线程池开启多线程一样。但由于我们的目的不是执行多线程，而是异步执行任务，所以一般需要另外一个线程就够了。

因此区别于执行多线程任务的我们常用的newFixedThreadPool，在执行异步任务时，我们用newSingleThreadExecutor 来创建一个单个线程的线程池

```java
public static void main(String[] args) throws Exception{

    System.out.println("主线程 =====> 开始 =====> " + System.currentTimeMillis());

    ExecutorService executorService = Executors.newSingleThreadExecutor();
    executorService.submit(()->{
        System.out.println("异步线程 =====> 开始 =====> " + System.currentTimeMillis());
        try{
            Thread.sleep(5000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("异步线程 =====> 结束 =====> " + System.currentTimeMillis());
    });
    executorService.shutdown(); // 回收线程池

    Thread.sleep(2000);

    System.out.println("主线程 =====> 结束 =====> " + System.currentTimeMillis());
    
}


主线程 =====> 开始 =====> 1627895467578
异步线程 =====> 开始 =====> 1627895467635
主线程 =====> 结束 =====> 1627895469644
异步线程 =====> 结束 =====> 1627895472649

```

## 三、通过@Async注解

使用起来也非常简单，将要异步执行的代码封装成一个方法，然后用@Async注解该方法，然后在主方法中直接调用就行

```java
@Test
public void mainThread() throws Exception{

    System.out.println("主线程 =====> 开始 =====> " + System.currentTimeMillis());
    collectionBill.asyncThread();
    Thread.sleep(2000);
    System.out.println("主线程 =====> 结束 =====> " + System.currentTimeMillis());

    Thread.sleep(4000); // 用于防止jvm停止，导致异步线程中断
}
@Async
public void asyncThread(){
    System.out.println("异步线程 =====> 开始 =====> " + System.currentTimeMillis());
    try{
        Thread.sleep(5000);
    }catch (InterruptedException e){
        e.printStackTrace();
    }
    System.out.println("异步线程 =====> 结束 =====> " + System.currentTimeMillis());
}

主线程 =====> 开始 =====> 1627897539948
异步线程 =====> 开始 =====> 1627897539956
主线程 =====> 结束 =====> 1627897541965
异步线程 =====> 结束 =====> 1627897544966

```

有以下两点需要注意：

1.类似@Tranctional注解，@Async注解的方法与调用方法不能在同一个类中，否则不生效
2.JUnit框架的设计不考虑多线程场景，所以主线程退出后，子线程也会跟着立即退出，所以可以在后面加多线程休眠时间来观察异步线程的执行情况

## 四、通过CompletableFuture

CompletableFuture是JDK1.8的新特性，是对Future的扩展。CompletableFuture实现了CompletionStage接口和Future接口，增加了异步回调、流式处理、多个Future组合处理的能力

```java
public static void main(String[] args) throws Exception{

    System.out.println("主线程 =====> 开始 =====> " + System.currentTimeMillis());

    ExecutorService executorService = Executors.newSingleThreadExecutor();
    CompletableFuture.runAsync(() ->{
        System.out.println("异步线程 =====> 开始 =====> " + System.currentTimeMillis());
        try{
            Thread.sleep(5000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("异步线程 =====> 结束 =====> " + System.currentTimeMillis());
    },executorService);
    executorService.shutdown(); // 回收线程池

    Thread.sleep(2000);

    System.out.println("主线程 =====> 结束 =====> " + System.currentTimeMillis());
    
}

主线程 =====> 开始 =====> 1627898354914
异步线程 =====> 开始 =====> 1627898354977
主线程 =====> 结束 =====> 1627898356980
异步线程 =====> 结束 =====> 1627898359979
```
