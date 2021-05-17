## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	JUC队列
&nbsp;  &nbsp;  &nbsp;  &nbsp; 单线程编程时我们会使用到一些集合类，如ArrayList等。ArrayList不是线程安全的，Vector通过加snychronized锁的方式实现线程安全，但是效率很低，同时Collections.synchronizedList(List<T> list)方法也能够将ArrayList转换为线程安全的。这些方法都很低效，都是串行执行的。下面介绍阻塞队列与非阻塞队列。
<br>


### （一）	非阻塞队列ConcurrentLinkedQueue

&nbsp;  &nbsp;  &nbsp;  &nbsp; 一个基于链接节点的无边界的线程安全队列，遵循队列的FIFO原则，采用CAS算法实现的，理论上的无边界队列，不过还是要受限与硬件。要尽量避免使用该队列的size()方法，因为它是由链表实现的，会遍历一遍队列，非常慢。当我们要检查队列中是否还有元素时，用poll()弹出队首元素或者用isEmpty()检查。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 这个队列只能保证我们在对这个队列进行单个操作时是线程安全的，如果要同时进行多个操作就需要自己来加锁保证线程的安全。
<br>



### （二）	阻塞队列BlockingQueue

#### 1.	BlockingQueue
&nbsp;  &nbsp;  &nbsp;  &nbsp; 阻塞队列接口，在某些情况下对阻塞队列的访问可能会造成阻塞，被阻塞的情况有以下两种：

&nbsp;  &nbsp;  &nbsp;  &nbsp; 队列满了的情况下进行入队操作；

&nbsp;  &nbsp;  &nbsp;  &nbsp; 队列空的情况下进行出队操作；
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp; 阻塞队列接口对插入，删除，获取元素操作提供了不同的方法，如下所示：



| 操作 |  抛出异常	|特殊值|	阻塞|	超时
|-|-|-|-|-|
插入|	add(e)	|offer(e)|	put(e)	|offer(e, time, unit)|
移除|	remove()	|poll()	|take()	|poll(time, unit)
检查	|element()	|peek()	|不可用	|不可用
<br>



#### 2.	ArrayBlockingQueue

##### （1）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这是阻塞队列接口的实现类，具体是由数组实现的有界阻塞队列，遵循FIFO原则。该队列是有界且固定的，因为是数组实现的，在构造时大小已经决定了。该队列对于等待的生产者和消费者线程的排序可以选择公平策略和非公平策略。
<br>



##### （2）	实现

&nbsp;  &nbsp;  &nbsp;  &nbsp; ArrayBlockingQueue继承AbstractQueue，实现BlockingQueue接口，内部使用可重入锁ReentrantLock+Condition实现多线程环境的并发操作，在该类中有以下参数：

&nbsp;  &nbsp;  &nbsp;  &nbsp; items: 一个定长数组，维护ArrayBlockingQueue中的元素；

&nbsp;  &nbsp;  &nbsp;  &nbsp; takeIndex: 队首的位置；

&nbsp;  &nbsp;  &nbsp;  &nbsp; putIndex: 队尾的位置；

&nbsp;  &nbsp;  &nbsp;  &nbsp; count: 元素个数

&nbsp;  &nbsp;  &nbsp;  &nbsp; lock: 锁，出队和入队都需要锁，两个步骤公用一个锁；
<br>



```java
public class ArrayBlockingQueue<E> extends AbstractQueue<E> implements BlockingQueue<E>, Serializable {
    private static final long serialVersionUID = -817911632652898426L;
    final Object[] items;
    int takeIndex;
    int putIndex;
    int count;
    // 重入锁
    final ReentrantLock lock;
    // notEmpty condition
    private final Condition notEmpty;
    // notFull condition
    private final Condition notFull;
    transient ArrayBlockingQueue.Itrs itrs;
}
```
<br>



##### （3）	实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过经典的生产者和消费者实例来测试数组阻塞队列。

```java
package thread10;

import java.util.Random;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.TimeUnit;

public class ArrayBlockingQueueDemo {
    private static ArrayBlockingQueue<Integer> queue =  new ArrayBlockingQueue<>(5, true);

    public static void main(String[] args) {
        Thread t1 = new Thread(new ProducerTask());
        Thread t2 = new Thread(new ConsumerTask());
        t1.start();
        t2.start();
    }

    //生产者线程
    static class ProducerTask implements Runnable{

        private Random rnd = new Random();

        @Override
        public void run() {
            try {
                while(true){
                    int value = rnd.nextInt(100);
                    queue.put(value);
                    System.out.println("生产者" +value);
                    TimeUnit.MILLISECONDS.sleep(100);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    //消费者
    static class ConsumerTask implements Runnable{
        @Override
        public void run(){
            try {
                while(true){
                    Integer value = queue.take();
                    System.out.println("消费者"+ value);
                    TimeUnit.MILLISECONDS.sleep(10);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
<br>



#### 3.	LinkedBlockingQueue

&nbsp;  &nbsp;  &nbsp;  &nbsp; 基于链表的有界阻塞队列，队列中的锁是分离的，入队的锁是putLock，出队的锁是takeLock。数组阻塞队列直接将枚举对象插入或者移除，而链表阻塞队列需要将枚举对象转换为Node后再进行插入或移除操作，这样会影响性能。
<br>



#### 4.	PriorityBlockingQueue

##### （1）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 优先级队列，始终保证出队的元素时优先级最高的元素，并且可以定制优先级的规则。内部使用二叉堆，通过二叉树最小堆算法来维护内部的数组，这个数组是可扩容的，若当前的元素个数大于最大容量时会通过算法扩容，扩容时其他的线程是不能进行出队操作的，实际上使用了先释放锁，然后通过CAS保证同时只有一个线程扩容。
<br>



##### （2）	特点
&nbsp;  &nbsp;  &nbsp;  &nbsp; 既然优先队列中需要排序，那么存储的对象就必须是可比较的对象，必须实现Comparable接口，不允许空值。优先队列的头是排序最小的元素，如果多个对象有同样的排序，则随机选取其中一个，返回队列的头对象。优先队列的大小是不受限制的，但在创建时可以指定初始大小，当向队列中增加元素时，队列大小会自动增加。

<br>


##### （3）	实例

&nbsp;  &nbsp;  &nbsp;  &nbsp; 创建一个自定义的对象类实现Comparable接口，传入PriorityBlockingQueue中。

```java
package thread11;

import java.util.Random;
import java.util.concurrent.PriorityBlockingQueue;

public class PriorityBlockingQueueDemo {
    public static void main(String[] args) throws InterruptedException {
        PriorityBlockingQueue<User> queue = new PriorityBlockingQueue<>();
        PriorityDemo demo = new PriorityDemo(queue);

        for(int i = 0; i < 3; ++i){
            new Thread(demo).start();
        }

        Thread.sleep(100);
        User u = queue.poll();
        while(u != null){
            System.out.println("优先级是" + u.getPriority() + "," + u.getUsername());
            u = queue.poll();
        }
    }

    static class PriorityDemo implements Runnable{

        PriorityBlockingQueue queue;
        Random r = new Random();

        public PriorityDemo(PriorityBlockingQueue queue){
            this.queue = queue;
        }
        @Override
        public void run() {
            for(int i = 0; i < 3; ++i){
                User user = new User();
                user.setPriority(r.nextInt(100));
                user.setUsername("张三" + i);
                queue.add(user);
            }
        }
    }

    static class User implements Comparable<User>{

        private Integer priority;
        private String username;

        @Override
        public int compareTo(User user) {
            return this.priority.compareTo(user.getPriority());
        }

        public Integer getPriority() {
            return priority;
        }

        public void setPriority(Integer priority) {
            this.priority = priority;
        }

        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }
    }
}
```
<br>



#### 5.	synchronousQueue

##### （1）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同步队列，实际上不是一个真正的队列，因为它不会为队列中的元素维护存储空间，它维护的是一组线程，这些线程等待着将元素加入或移除队列。由于该队列没有存储功能，队列中至多有一个元素，因此put入队操作会一直阻塞，直到有take出队操作执行。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 同步队列是用于直接交付操作的，所谓的直接交付指的是直接将任务交到消费者的手上，而不是放到队列中等待消费者来消费。因为同步队列中没有存储能力，所以消费者能够直接得到生产者生产的任务，降低了将数据从生产者移动到消费者的延迟。也正是因为没有存储能力，同步队列只适合于存在多个消费者且总是有一个消费者处于准备状态的情况，这样生产者的任务才能够被消费者消费。
<br>



##### （2）	特点

&nbsp;  &nbsp;  &nbsp;  &nbsp; 阻塞队列，每个put必须等待一个take。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 线程安全的，阻塞的。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 不允许使用null元素
<br>



##### （3）	put和offer
&nbsp;  &nbsp;  &nbsp;  &nbsp; put()方法将元素放入队列后，会一直等待直到有其他线程将这个元素取走；

&nbsp;  &nbsp;  &nbsp;  &nbsp; offer()方法将元素放入队列后，直接返回，如果有其他线程取走了元素则返回true，否则返回false;
<br>



##### （4）	take和poll

&nbsp;  &nbsp;  &nbsp;  &nbsp; take()取出并且remove掉queue中的element，取不到会一直等待；

&nbsp;  &nbsp;  &nbsp;  &nbsp; poll()取出并且remove掉queue中的element，取到了就返回element，否则返回null。
<br>






