# Current Programming

```markdown
**1. sleep 和 wait 区别**
sleep:
- 位置：属于Thread类中的方法
- 是否释放锁：释放 cpu 给其它线程 不释放锁资源
wait:
- 位置：属于Object类中的方法
- 是否释放锁：释放 cpu 给其它线程，同时释放锁资源
- wait() 一直等待需要通过 notify 或者 notifyAll 进行唤醒

**2. 创建线程的方式**
- 继承Thread
- 实现Runnable
- Callable和Future创建，有返回值
- 线程池

**3. 线程有哪些状态**
创建，运行，阻塞，死亡

**4. 为什么使用线程池？**
避免线程重复创建，线程复用，提高资源利用率，便于管理

**5. 如何创建线程池?**

```
ThreadPoolExecutor threadPool = new ThreadPoolExecutor(5, 10, 1,
TimeUnit.MINUTES, new LinkedBlockingDeque<>(300), new ThreadPoolExecutor.DiscardPolicy());
线程池运行原理
核心线程：先用核心线程
最大线程：队列满了，运行最大线程
队列：然后往队列里放
拒绝策略：拒绝，丢弃，使用当前线程处理
AbortPolicy：丢弃任务并抛出 RejectedExecutionException 异常，这是默认的策略。
DiscardPolicy：也是丢弃任务，但是不抛出异常，相当于静默处理。
DiscardOldestPolicy：丢弃队列最前面的任务，执行后面的任务
CallerRunsPolicy：由调用线程处理该任务
```

**6. CompletableFuture多线程处理**
应用：私域PC端关于数据的并行计算

**7.锁的分类**

 - 乐观锁： CAS锁
 - 悲观锁：synchronized，ReentrantLock

**8. CAS乐观锁原理**

1.读取共享资源中的值保存至线程的本地变量中，
2.比较之前读取的值和目前的值是否相同，如果相同的话，尝试写入；如果不同就重试；不断循环，直到成功

**9. synchronized和ReentrantLock区别？**

- 内置和显式的区别： synchronized是内置锁，可以直接用；ReentranLock需要显式的创建和管理锁对象
- 功能性：ReentrantLock类似自动挡，可自定义的东西更多（公平性，可中断）

**10. synchronized 和 volatile 有什么区别**

- 性质：synchronized是锁，可以保证线程的互斥，volatile只能保证变量的可见性
- 性能：synchronized开销更大

**11. 如何实现线程同步？**

使用锁：

- 关键字synchronized
- ReentrantLock
  
**12. 线程池处理超时问题如何解决？**

- 创建线程池
- 提交任务获取Future对象
- 通过Future的get方法设置超时时间

```
ThreadPoolExecutor customThreadPool = new ThreadPoolExecutor(corePoolSize, maxPoolSize, keepAliveTime, TimeUnit.SECONDS, workQueue);

Future<String> future = customThreadPool.submit(() -> {
    // 执行耗时任务
    return "Task Result";
});

try {
    String result = future.get(2, TimeUnit.SECONDS); // 设置超时时间为2秒
    // 处理任务结果
} catch (TimeoutException e) {
    // 处理超时异常
    // 可以选择取消任务执行
    future.cancel(true);
} catch (InterruptedException | ExecutionException e) {
    // 处理其他异常
    e.printStackTrace();
}
```

**13. synchronized底层实现原理**

- 锁的信息是存储在Java对象头中
- 信息中包含一个锁的标志位来表示锁的状态
- 锁的标志位的4种状态：
  - 无锁：初始状态
  - 偏向锁：第一个线程尝试获取锁时，锁状态升级为偏向锁。
  - 轻量级锁：当有其他线程想要获取锁时，锁状态升级为轻量级锁。线程通过CAS来获取锁，若失败，锁状态升级为重量级锁。
  - 重量级锁：没有获取到锁的线程会被操作系统挂起，等锁释放会被重新唤起。

```