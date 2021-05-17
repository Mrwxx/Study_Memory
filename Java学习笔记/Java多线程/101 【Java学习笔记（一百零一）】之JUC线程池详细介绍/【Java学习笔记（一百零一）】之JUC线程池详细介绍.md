## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	JUC线程池

### （一）	优势

1.	降低资源消耗，通过重用已创建的线程来降低线程创建和销毁的性能损失；

3.	提高响应速度，任务无需创建线程，直接执行；

5.	提高线程的可管理性，统一管理，分配，调优和监控。
<br>



### （二）	线程池状态

&nbsp;  &nbsp;  &nbsp;  &nbsp; AtomicInteger类型的变量ctl中定义了线程池中的任务数量和线程池的状态两个信息，共32位，其中高3位表示线程池状态，低29位表示线程池中的任务数量。线程池有五种状态，如下所示：
<br>



#### 1.	RUNNING
&nbsp;  &nbsp;  &nbsp;  &nbsp; 处于该状态下线程池能够接受新的任务，以及对新添加的任务进行处理。
<br>



#### 2.	SHUTDOWN
&nbsp;  &nbsp;  &nbsp;  &nbsp; 处于该状态的线程池不可以接受新的任务，但是可以对已添加的任务进行处理。通过调用shutdown()函数进入该状态。
<br>



#### 3.	STOP
&nbsp;  &nbsp;  &nbsp;  &nbsp; 处于该状态的线程池不接受新的任务，也不处理已添加的任务，直接中断正在处理的任务。通过调用shutdownNow()函数进入该状态，这个函数尽量不要使用，因为会中断正在执行的线程。
<br>



#### 4.	TIDYING
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当所有的任务终止，ctl记录的任务数量为0时，线程池变为TIDYING状态。之后会执行钩子函数terminated()，该函数在ThreadPoolExecutor类中是空的，若用户想在线程池变为TIDYING时进行相应的处理，可以重载terminated()函数。
<br>



####  5.	TERMINATED
&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程池彻底终止。
<br>



### （三）	构造方法

&nbsp;  &nbsp;  &nbsp;  &nbsp; Java的线程池支持主要是通过ThreadPoolExecutor类实现的，它的构造方法有七个参数，如下所示：
<br>



#### 1.	corePoolSize
&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程池中核心线程的数量（线程池的基本大小），当提交一个任务时，线程池会新建一个线程来执行任务，直到当前线程数等于corePoolSize。如果调用了prestartAllCoreThreads()方法，线程池会提前创建并启动所有基本线程。
	<br>



#### 2.	maximumPoolSize
&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程池中允许的最大线程数，若线程池的阻塞队列满了后，还有任务提交，只要当前的线程数小于maximumPoolSize，就会创建线程来执行任务。
<br>



#### 3.	keepAliveTime
&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程的存活时间，由于线程的创建和销毁是需要代价的，在执行完任务后不会立即销毁，而是存活一段时间，默认情况下，该参数只有在线程数大于corePoolSize时才会生效，即只有大于核心线程数时才会销毁多余的线程。

<br>


#### 4.	unit
&nbsp;  &nbsp;  &nbsp;  &nbsp; keepAliveTime的单位，TimeUnit。
<br>



#### 5.	workQueue
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当任务超过了maximumPoolSize时，就会将等待的任务保存在 workQueue中，等待的任务必须实现Runnable接口，队列有以下几种：

&nbsp;  &nbsp;  &nbsp;  &nbsp; ArrayBlockingQueue: 基于数组的有界阻塞队列，FIFO；
&nbsp;  &nbsp;  &nbsp;  &nbsp; LinkedBlockingQueue: 基于链表的有界阻塞队列，FIFO；
&nbsp;  &nbsp;  &nbsp;  &nbsp; PriorityBlockingQueue: 具有优先级别的阻塞队列，
&nbsp;  &nbsp;  &nbsp;  &nbsp; SynchronousQueue: 不存储元素的阻塞队列；
<br>



#### 6.	threadFactory

&nbsp;  &nbsp;  &nbsp;  &nbsp; 用于设置创建线程的工厂，作用是提供创建线程的功能的线程工厂，通过newThread()方法提供创建线程的功能，该方法创建的线程都是非守护线程，而且线程优先级都是默认优先级。
<br>



#### 7.	handler
&nbsp;  &nbsp;  &nbsp;  &nbsp; RejectedExecutionHandler，线程池的拒绝策略，所谓拒绝策略是指将任务添加到线程池中时，若次线程池已经饱和，且阻塞队列也已经满了，那么线程池就会选择一种拒绝策略来拒绝该任务。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程池提供了四种拒绝策略：

&nbsp;  &nbsp;  &nbsp;  &nbsp; AbortPolicy: 直接抛出异常，默认策略；

&nbsp;  &nbsp;  &nbsp;  &nbsp; CallerRunsPolicy: 用调用者所在的线程来执行任务；

&nbsp;  &nbsp;  &nbsp;  &nbsp; DiscardOlestPolicy: 丢弃阻塞队列中最靠前的任务，并执行当前任务；

&nbsp;  &nbsp;  &nbsp;  &nbsp; DiscardPolicy: 直接丢弃任务；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当然，我们也可以实现自己的拒绝策略，如日志记录等，只要实现RejectedExecutionHandler接口即可。

<br>



### （四）	四种线程池

&nbsp;  &nbsp;  &nbsp;  &nbsp; 除了使用ThreadPoolExecutor根据实际情况创建线程池以外，Executor框架也提供了三种线程池 ，都可以通过工具类Executors来创建。还有一种线程池ScheduledThreadPoolExecutor，相当于提供了“延迟”和“周期 执行”功能的ThreadPoolExecutor。
<br>



#### 1.	FixedThreadPool

##### （1）	特点
&nbsp;  &nbsp;  &nbsp;  &nbsp; FixedThreadPool是复用固定数量的线程来处理一个共享的无边界队列，在创建该线程池时，指定了线程数量，corePoolSize和maximumPoolSize都设置为了该数字，则当线程池中的线程数量等于corePoolSize时，如果继续提交任务，就会被添加到阻塞队列workQueue中，而workQueue使用的是LinkedBlockingQueue，但是没有设置范围，则是Integer.MAX_VALUE范围，相当于无界队列。
<br>



##### （2）	定义
该线程池的定义如下：

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 从定义中可以看出，线程的存活时间为0，因为corePoolSize和maximumPoolSize是相等的，因此不会有线程数大于corePoolSize的情况，因此也不会销毁线程。当该线程池中的线程数小于corePoolSize时，会创建新的线程来执行其他的任务；当线程数量到达了corePoolSize时，不同的任务会复用该线程池中的固定线程，如果有等待的任务则会进入阻塞队列中等待。
<br>



##### （3）	实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建固定3个线程的线程池，通过5个线程提交任务，可以看到线程池中的线程是复用的。

```java
package thread12;

import thread.Run;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPool {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService exec = Executors.newFixedThreadPool(3);
        for(int i = 0 ; i < 5; ++i){
            exec.execute(new Demo());
            Thread.sleep(10);
        }
        exec.shutdown();

    }

    static class Demo implements Runnable{

        @Override
        public void run() {
            String name = Thread.currentThread().getName();
            for(int i = 0 ; i < 2; ++i){
                System.out.println(name + ":" + i);
            }
        }
    }
}
```

<br>



#### 2.	SingleThreadExecutor

##### （1）	特点
&nbsp;  &nbsp;  &nbsp;  &nbsp; 只是用单个工作线程来执行一个无边界的队列。
<br>



##### （2）	定义
该线程池的定义如下：

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，corePoolSize和maximumPoolSize都设置为1，因此只有单个线程执行任务。同时线程存活时间设置为0，因为不需要没有空闲线程需要销毁。使用的阻塞队列时LinkedBlockingQueue无界的链表阻塞队列。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果单个线程在执行任务过程中终止了，则新的线程会代替它来执行后续的线程。
<br>



#### 3.	CachedThreadPool
##### （1）	特点
&nbsp;  &nbsp;  &nbsp;  &nbsp; 该线程池会根据任务的需要，在线程可用时，重用之前构造好的池中线程，否则创建新的线程。
<br>



##### （2）	定义

该线程池的定义如下所示：

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，corePoolSize设置为0，maximumPoolSize设置为Integer.MAX_VALUE，表示能够创建无穷多的线程，只要任务提交就会加入到阻塞队列中，只要线程池中没有线程，就会创建线程来执行任务。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程的存活时间设置60秒，即当空闲的线程等待新任务的时间超过60秒时会被终止，阻塞队列采用 SynchronousQueue，这是一个不能存储元素的阻塞队列。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 因此，该线程池适合执行大量短生命周期的异步任务，不同的任务会重用之前构造的线程，同时线程池即使在长时间空闲也不会消耗任何资源，因为不论是线程还是阻塞队列中都没有存储资源。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 不过，这种线程池也存在问题，如果提交任务的速度远远大于线程池的处理速度，那么线程池就会不断地创建新的线程来执行任务，这样会消耗大量的CPU和内存资源，因此使用该线程池需要控制并发的任务数。

<br>



#### 4.	ScheduledThreadPool
##### （1）	Timer和TimerTask的问题

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在实现周期线程和延迟调度时可以使用Timer和TimerTask，但是它们存在一些问题：

&nbsp;  &nbsp;  &nbsp;  &nbsp; Timer在执行定时任务时只会创建一个线程，如果存在多个任务，且任务时间过长，超过两个任务的间隔时间会发生问题；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果TimerTask抛出RuntimeException，Timer会停止所有任务的运行；

&nbsp;  &nbsp;  &nbsp;  &nbsp; Timer执行周期任务时依赖于系统时间，如果系统时间出现问题则执行上也会有问题。
<br>



##### （2）	实现

&nbsp;  &nbsp;  &nbsp;  &nbsp; ScheduledThreadPoolExecutor，继承了ThreadPoolExecutor，且实现了ScheduledExecutorService接口，相当于提供了延迟和周期执行功能的ThreadPoolExecutor。它可以安排在给定的延迟时间后执行命令，或者定期执行命令，灵活性优于Timer。

```java
   public ScheduledThreadPoolExecutor(int corePoolSize,
                                       ThreadFactory threadFactory,
                                       RejectedExecutionHandler handler) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
                new DelayedWorkQueue(), threadFactory, handler);
    }
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，该线程池内部通过继承类ThreadLocalExecutor的构造方法实现的，指定了corePoolSize，maximumPoolSize设置为最大值。使用的阻塞队列变为了DelayedWorkQueue，这个队列类似于延时队列和优先级队列。在执行定时任务时，每个任务的执行时间都不同，所以该队列的任务就是按照执行时间的升序排列，执行时间距离当前时间越近的任务排在靠前的位置，保证按照执行时间执行。

<br>


##### （3）	实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建三个延迟任务，检验延迟效果。

```java
package thread13;

import thread.Run;

import java.util.Date;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ThreadPool2 {
    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(2);
        System.out.println("程序开始 "+ new Date());
        scheduledExecutorService.schedule(new Task(), 0, TimeUnit.SECONDS);
        scheduledExecutorService.schedule(new Task(), 1, TimeUnit.SECONDS);
        scheduledExecutorService.schedule(new Task(), 5, TimeUnit.SECONDS);

        Thread.sleep(5000);
    }

    static class Task implements Runnable{

        @Override
        public void run() {
            try {
                String name = Thread.currentThread().getName();
                System.out.println(name + " 开始 " + new Date());
                Thread.sleep(1000);
                System.out.println(name+ "结束 " + new Date());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```








