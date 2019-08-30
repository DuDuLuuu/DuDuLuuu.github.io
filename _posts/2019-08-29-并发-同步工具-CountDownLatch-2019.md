---
layout:     post
title:      "并发-同步工具-CountDownLatch"
subtitle:   " \"keep hungry keep foolish\""
date:       2019-08-29 12:00:00
author:     "Bz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 并发
    

## CountDownLatch：
---


```
/**
 * CountDownLatch：某线程调用await()阻塞，直到其他线程调用countDown()累计到指定参数次数才唤醒被阻塞线程
 *
 * @author JDb
 */
public class CountDownLatchDemo {

    public static void main(String[] args) {
        Integer totalThreadCount = 5;
        Integer stopCount = 2;
        ExecutorService threadPool = Executors.newFixedThreadPool(totalThreadCount);
        final CountDownLatch countDownLatch = new CountDownLatch(stopCount);
        for (int i = 0; i < totalThreadCount; i++) {
            final int index=i;
            threadPool.execute(() -> {
                String currentThread = Thread.currentThread().toString();
                System.out.println("当前线程:"+currentThread+",开始");

                try {
                    TimeUnit.SECONDS.sleep(2);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if (index <stopCount) {
                    //累加
                    countDownLatch.countDown();
                    System.out.println("--->当前线程:"+currentThread+",调用 countDown()");
                }
                try {
                    TimeUnit.SECONDS.sleep(2);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("--->当前线程:"+currentThread+",结束");

            });
        }
        System.out.println("==============等待"+stopCount+"个线程调用CountDown()：当前线程阻塞==============");
        try {
            //进入阻塞
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("=============="+stopCount+"个线程调用CountDown()：当前线程唤醒==============");
        threadPool.shutdown();
    }
}
```
### 运行结果

```
当前线程:{Thread[pool-1-thread-4,5,main]},开始
当前线程:{Thread[pool-1-thread-5,5,main]},开始
当前线程:{Thread[pool-1-thread-2,5,main]},开始
==============等待{2}个线程调用CountDown()：当前线程阻塞==============
当前线程:{Thread[pool-1-thread-3,5,main]},开始
当前线程:{Thread[pool-1-thread-1,5,main]},开始
--->当前线程:{Thread[pool-1-thread-1,5,main]},调用 countDown()
--->当前线程:{Thread[pool-1-thread-2,5,main]},调用 countDown()
=============={2}个线程调用CountDown()：当前线程唤醒==============
--->当前线程:{Thread[pool-1-thread-3,5,main]},结束
--->当前线程:{Thread[pool-1-thread-2,5,main]},结束
--->当前线程:{Thread[pool-1-thread-5,5,main]},结束
--->当前线程:{Thread[pool-1-thread-4,5,main]},结束
--->当前线程:{Thread[pool-1-thread-1,5,main]},结束

```

## 项目地址
https://github.com/DuDuLuuu/Java