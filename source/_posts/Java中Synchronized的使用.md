---
title: Java中Synchronized的使用
date: 2022-08-25 09:21:17
author: jason.xiang
tags: 
- java
- 多线程
---

在编程中，经常需要用到同步，这里讲一下synchronized关键字的相关知识

# 1.使用方式

- 修饰一个**代码块**，被修饰的代码块称为同步代码块，作用范围是大括号{}括起来的代码；
- 修饰一个**方法**，被修饰的方法称为同步方法，其作用范围是整个方法；
- 修改一个**静态方法**，作用范围是整个静态方法；
- 修改一个**类**，作用范围是synchronized后面括号括起来的部分。

# 2.使用示例

**修饰代码块**
注：this 表示锁是当前对象，锁可以自定义，但是要实现同步必须是同一把锁才可以。

```java
public void method()
{
   synchronized(this) {
     // todo some thing 
   }
}
```

**修饰方法**

```java
public synchronized void method()
{
   // todo some thing
}
```

修饰方法的注意事项

- 接口方法时不能使用synchronized关键字;
- 构造方法不能使用synchronized关键字，但可以使用synchronized代码块进行同步;
- synchronized关键字无法继承;
  如果在父类中的某个方法使用了synchronized关键字，而在子类中覆盖了这个方法，在子类中的这个方法默认情况下并不是同步的。
  子类方法同步的解决方案
  1）子类方法也加上synchronized 关键字
  2）子类方法中调用父类同步的方法，例如：使用 super.xxxMethod()调用父类方法

**修饰静态方法**

```java
public synchronized static void method() {
   // todo some thing
}
```

修饰类

```java
class DemoClass {
   public void method() {
      synchronized(DemoClass.class) {
         // todo some thing
      }
   }
}
```

# 3.实战实例

## 3.1 修饰代码块

```java
/**
 * synchrosnized 关键字测试
 * 同步代码块
 * @author 码农猿
 */
public class SynchronizedDemo1 implements Runnable {
    /**
     * 全局变量
     * 创建一个计数器
     */
    private static int counter = 1;

    @Override
    public void run() {
        Date startDate = DateUtil.date();
        synchronized (this) {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println("线程 ：" + Thread.currentThread().getName() + " 当前计数器 ：" + (counter++));
                    System.out.println("开始时间 ：" + startDate + " 当前时间 ：" + DateUtil.date());
                    System.out.println();
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    public static void main(String[] args) {
        SynchronizedDemo1 syncThread = new SynchronizedDemo1();
        Thread thread1 = new Thread(syncThread, "sync-thread-1");
        Thread thread2 = new Thread(syncThread, "sync-thread-2");
        thread1.start();
        thread2.start();
    }
}
```

**结果图示**
![img](http://image109.360doc.com/DownloadImg/2021/06/2911/225239904_1_20210629110511192)
**结果说明**
当两个并发线程(thread1和thread2)访问同一个对象(syncThread)中的synchronized代码时
在同一时刻只能有一个线程得到执行，
另一个线程受阻塞，必须等待当前线程执行完这个代码块以后才能执行该代码块。Thread1和thread2是互斥的，因为在执行synchronized代码块时会锁定当前的对象，只有执行完该代码块才能释放该对象锁，下一个线程才能执行并锁定该对象。

**稍加改动**

```java
public static void main(String[] args) {
        SynchronizedDemo1 syncThread1 = new SynchronizedDemo1();
        SynchronizedDemo1 syncThread2 = new SynchronizedDemo1();
        Thread thread1 = new Thread(syncThread1, "sync-thread-1");
        Thread thread2 = new Thread(syncThread2, "sync-thread-2");
        thread1.start();
        thread2.start();
    }
```

![img](http://image109.360doc.com/DownloadImg/2021/06/2911/225239904_2_20210629110513598) 从图上可以看出来，两个线程都是新建一个对象去执行的，所以锁也是两个，所以执行方式是同时执行了。

## 3.2修饰普通方法

Synchronized修饰一个方法很简单，就是在方法的前面加synchronized关键字，
修饰方法和修饰一个代码块类似，只是作用范围不一样，修饰代码块是大括号括起来的范围，而修饰方法范围是整个函数。

将第一个个实例中的run方法改成如下的方式，实现的效果一样。

```java
    @Override
    public synchronized void run() {
        Date startDate = DateUtil.date();
        for (int i = 0; i < 5; i++) {
            try {
                System.out.println("线程 ：" + Thread.currentThread().getName() + " 当前计数器 ：" + (counter++));
                System.out.println("开始时间 ：" + startDate + " 当前时间 ：" + DateUtil.date());
                System.out.println();
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
```

## 3.2修饰静态方法

```java
package com.example.lock.syn.demo02;

import cn.hutool.core.date.DateUtil;
import java.util.Date;

/**
 * synchrosnized 关键字测试
 * 同步-静态方法
 *
 * @author 码农猿
 */
public class SynchronizedDemo3 implements Runnable {
    private static int counter = 1;
    
    /**
     * 静态的同步方法
     */
    public synchronized static void method() {
        Date startDate = DateUtil.date();
        for (int i = 0; i < 5; i++) {
            try {
                System.out.println("线程 ：" + Thread.currentThread().getName() + " 当前计数器 ：" + (counter++));
                System.out.println("开始时间 ：" + startDate + " 当前时间 ：" + DateUtil.date());
                System.out.println();
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    @Override
    public void run() {
        method();
    }
}
```

测试代码

```java
    public static void main(String[] args) {
        SynchronizedDemo3 syncThread1 = new SynchronizedDemo3();
        SynchronizedDemo3 syncThread2 = new SynchronizedDemo3();
        Thread thread1 = new Thread(syncThread1, "sync-thread-1");
        Thread thread2 = new Thread(syncThread1, "sync-thread-2");
        thread1.start();
        thread2.start();
    }
```

**结果图示**
![img](http://image109.360doc.com/DownloadImg/2021/06/2911/225239904_3_20210629110513942)
**结果说明**
syncThread1和syncThread2是SyncThread的两个对象，但在thread1和thread2并发执行时却保持了线程同步。这是因为run中调用了静态方法method，而静态方法是属于同一类的，所以syncThread1和syncThread2相当于用了同一把锁。

注： 实现同步是要很大的系统开销作为代价的，甚至可能造成死锁，所以尽量避免无谓的同步控制
