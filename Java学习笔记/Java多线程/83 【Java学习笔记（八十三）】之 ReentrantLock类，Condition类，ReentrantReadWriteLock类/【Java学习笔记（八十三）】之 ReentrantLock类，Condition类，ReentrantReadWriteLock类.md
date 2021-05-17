## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Lock的使用

### （一）	ReentrantLock类
&nbsp;  &nbsp;  &nbsp;  &nbsp;之前我们使用了synchronized关键字实现线程之间的同步互斥，在JDK1.5中添加了ReentrantLock类也能够达到同样的效果，并且扩展了新的功能。
<br>


#### 1.	lock() 与 unlock()
&nbsp;  &nbsp;  &nbsp;  &nbsp;创建一个新的ReentrantLock锁对象，调用该锁对象的lock()方法获得锁，线程调用了该方法，就持有了对象监视器，其他线程只有等待锁被释放时再次争抢。同样的，调用该所对象的unlock()方法释放锁。

```cpp
private Lock lock = new ReentrantLock();
lock.lock();
lock.unlock();
```
<br>



### （二）	Condition类
&nbsp;  &nbsp;  &nbsp;  &nbsp;我们可以通过Condition对象实现等待/通知模式，这是在JDK5中出现的技术，有较好的灵活性，可以实现多路通知，即在一个Lock对象内创建多个Condition(对象监视器)实例，线程对象可以注册在指定的Condition中，从而可以有选择性地进行线程通知。而synchronized相当于整个Lock对象中只有一个单一的Condition对象，所有线程都注册在一个对象上，通知是随机的。

```cpp
private Lock lock = new ReentrantLock();
private Condition condition = lock.newCondition();
contion.await();
condition,signal();
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;首先在一个线程中调用Lock对象的lock()方法获取锁对象，然后调用Condition对象的await()方法，使当前执行任务的线程进入等待状态，最后调用Condition对象的signal()方法，通知该Condition对象处理等待中的线程。还有signalAll()方法是唤醒所有等待的线程。

&nbsp;  &nbsp;  &nbsp;  &nbsp;将不同的线程注册到不同的Condition对象中，通过调用不同的Condition对象的notifyAll()方法或者notify()方法就可以控制唤醒的线程对象。
<br>



### （三）	生产者/消费者模式
&nbsp;  &nbsp;  &nbsp;  &nbsp;与之前使用wait()/notify()一样，这里使用的是Condition对象的await()/signal(), signalAll()方法。
<br>



### （四）	公平锁与非公平锁
&nbsp;  &nbsp;  &nbsp;  &nbsp;锁对象Lock分为公平锁和非公平锁。公平锁表示线程获取锁的顺序是按照线程加锁的顺序来分配的，即先来先得的FIFO顺序，但是只能保证大多数是按照顺序的，总会有少数不按顺序分配的。而非公平锁就是随机获取锁的机制。

&nbsp;  &nbsp;  &nbsp;  &nbsp;在创建ReentrantLock锁对象的时候，传入一个Boolean值表示是否是公平锁：

```cpp
Lock lock = new ReentrantLock(true);
```
<br>



### （五）	Lock锁对象的方法
#### 1.	getHoldCount()
&nbsp;  &nbsp;  &nbsp;  &nbsp;该方法查询当前线程保持此锁对象的个数，也就是该锁对象调用lock()方法的次数。

```cpp
lock.getHoldCount();
```
<br>



#### 2.	getQueueLength()
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回正在等待获取此锁对象的线程估计数，如共有5个线程，当1个线程先执行了await()方法后，那么还有4个线程在等待着该锁对象的释放。

```cpp
lock.getQueueLength();
```
<br>



#### 3.	getWaitQueueLength(Condition condition)
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回等待与此锁对象相关的给定条件Condition的线程估计数，如有5个线程，每个线程都绑定在同一个Condition对象上，并且调用了该Condition对象的await()方法，则调用getWaitQueueLength(Condition condition)方法返回的是5。关键点是那个Conditino对象绑定的线程数量。

```cpp
lock.getWaitQueueLength(newCondition);
```

<br>


#### 4.	hasQueuedThread()
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询是否有线程正在等待获取此锁对象。

```cpp
lock.hasQueuedThread();
```
<br>



#### 5.	hasQueuedThread(Thread thread)
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询指定的线程是否正在等待获取此锁对象。

```cpp
lock.hasQueuedThread(thread);
```
<br>



#### 6.	hasWaiters(Condition condition)
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询是否有线程正在等待与此锁对象有关的Condition条件

```cpp
lock.hasWaiters(condition);
```
<br>



#### 7.	isFair()
&nbsp;  &nbsp;  &nbsp;  &nbsp;判断当前的锁对象是不是公平锁，默认的情况下，ReentrantLock类使用的是非公平锁。

```cpp
lock.isFair();
```

<br>


#### 8.	isHeldByCurrentThread()
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询当前线程是否保持着这个锁对象，这个方法通常用在释放锁对象的时候，先判断再释放。

```cpp
lock.isHeldByCurrentThread();
```
<br>



#### 9.	isLocked()
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询此锁对象是否由任意的线程保持着，即锁对象是否正在被使用。

```cpp
lock.isLocked();
```
<br>



#### 10.	lockInterruptibly()

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果当前线程未被终端，则可以获取该锁对象，如果已经被中断了，则出现异常。

```cpp
lock.lockInterruptibly();
```
<br>



#### 11.	Boolean tryLock()

&nbsp;  &nbsp;  &nbsp;  &nbsp;当某线程调用该方法时，只有在调用时锁对象未被另一个线程持有的情况下，才能够获取该锁对象。

```cpp
lock.tryLock();
```

<br>


####  12.	Boolean tryLock(long timeout, TimeUnit unit)

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果锁对象在给定的等待时间内没有被另一个线程获取，且当前线程未被中断，则获取该锁对象。

```cpp
lock.tryLock(3, TimeUnit.SECONDS);
```
<br>



### （六）	Condition对象的方法
#### 1.	awaitUninterruptibly()
&nbsp;  &nbsp;  &nbsp;  &nbsp;与await()方法的区别是，当前线程调用该方法后，即使线程被中断了，也不会出现异常，而await()方法被中断时，立即会出现异常。

```cpp
condition.awaitUninterruptibly();
```
<br>



#### 2.	awaitUntil(Time)
&nbsp;  &nbsp;  &nbsp;  &nbsp;进入等待状态Time时间，之后会自动唤醒。也可以由其他的线程来提前唤醒。

```cpp
condition.awaitUntil(Time);
```
<br>



### （七）	ReentrantReadWriteLock类

#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;虽然ReentrantLock类能够实现完全互斥的功能，但是同一时刻只有一个线程在执行ReentrantLock.lock()后面的任务，十分低效。因此，引出了一个新的读写锁ReentrantReadWriteLock类。
<br>



#### 2.	读锁与写锁
&nbsp;  &nbsp;  &nbsp;  &nbsp;读写锁有两个锁，一个是与读操作相关的锁，称为共享锁；一个是与写操作相关的锁，称为排它锁。很显然，多个读锁之间不是互斥的，读锁与写锁是互斥的，多个写锁是互斥的。
<br>



#### 3.	读锁

```cpp
private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
lock.readLock().lock();
lock.readLock().unlock();
```
<br>



#### 4.	写锁

```cpp
private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
lock.writeLock().lock();
lock.writeLock().unlock();
```







