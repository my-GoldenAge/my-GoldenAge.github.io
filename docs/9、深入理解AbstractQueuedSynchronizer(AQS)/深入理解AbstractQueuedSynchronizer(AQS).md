# 

# 1、AQS简介

在上一篇文章中我们对lock和AbstractQueuedSynchronizer(AQS)有了初步的认识。在同步组件的实现中，AQS是核心部分，同步组件的实现者通过使用AQS提供的模板方法实现同步组件语义，AQS则实现了对**同步状态的管理，以及对阻塞线程进行排队，等待通知**等等一些底层的实现处理。AQS的核心也包括了这些方面：**同步队列，独占式锁的获取和释放，共享锁的获取和释放以及可中断锁，超时等待锁获取这些特性的实现**，而这些实际上则是AQS提供出来的模板方法，归纳整理如下：

**独占式锁：**

> void acquire(int arg)：独占式获取同步状态，如果获取失败则插入同步队列进行等待； 
>
> void acquireInterruptibly(int arg)：与acquire方法相同，但在同步队列中进行等待的时候可以检测中断； 
>
> boolean tryAcquireNanos(int arg, long nanosTimeout)：在acquireInterruptibly基础上增加了超时等待功能，在超时时间内没有获得同步状态返回false; 
>
> boolean release(int arg)：释放同步状态，该方法会唤醒在同步队列中的下一个节点

**共享式锁：**

> void acquireShared(int arg)：共享式获取同步状态，与独占式的区别在于同一时刻有多个线程获取同步状态； 
>
> void acquireSharedInterruptibly(int arg)：在acquireShared方法基础上增加了能响应中断的功能； 
>
> boolean tryAcquireSharedNanos(int arg, long nanosTimeout)：在acquireSharedInterruptibly基础上增加了超时等待的功能； 
>
> boolean releaseShared(int arg)：共享式释放同步状态

要想掌握AQS的底层实现，其实也就是对这些模板方法的逻辑进行学习。在学习这些模板方法之前，我们得首先了解下AQS中的同步队列是一种什么样的数据结构，因为同步队列是AQS对同步状态的管理的基石。

# 2、同步队列

当共享资源被某个线程占有，其他请求该资源的线程将会阻塞，从而进入同步队列。就数据结构而言，队列的实现方式无外乎两者一是通过数组的形式，另外一种则是链表的形式。AQS中的同步队列则是**通过链式方式**进行实现。接下来，很显然我们至少会抱有这样的疑问：

1. 节点的数据结构是什么样的？
2. 是单向还是双向？
3. 是带头结点的还是不带头节点的？

我们依旧先是通过看源码的方式：

在AQS有一个静态内部类Node，其中有这样一些属性：

> volatile int waitStatus; //节点状态 
>
> volatile Node prev; //当前节点/线程的前驱节点 
>
> volatile Node next; //当前节点/线程的后继节点 
>
> volatile Thread thread; //加入同步队列的线程引用 
>
> Node nextWaiter; //等待队列中的下一个节点

节点的状态有以下这些：

> int CANCELLED = 1; //节点从同步队列中取消 
>
> int SIGNAL = -1; //后继节点的线程处于等待状态，如果当前节点释放同步状态会通知后继节点，使得后继节点的线程能够运行； 
>
> int CONDITION = -2; //当前节点进入等待队列中 
>
> int PROPAGATE = -3; //表示下一次共享式同步状态获取将会无条件传播下去 
>
> int INITIAL = 0; //初始状态

节点是构成同步队列的基础，同步器拥有首节点（head）和尾节点（tail），没有成功获取同步状态的线程将会成为节点加入该队列的尾部，同步队列的基本结构如图所示：

 ![image-20220404174332802](img-深入理解AbstractQueuedSynchronizer(AQS)/image-20220404174332802.png)

如图，同步器包含了两个节点类型的引用，一个指向头节点，而另一个指向尾节点。试想一下，当一个线程成功地获取了同步状态（或者锁），其他线程将无法获取到同步状态，转而被构造成为节点并加入到同步队列中，而这个加入队列的过程必须要保证线程安全，因此同步器提供了一个基于CAS的设置尾节点的方法：compareAndSetTail(Node expect,Nodeupdate)，它需要传递当前线程“认为”的尾节点和当前节点，只有设置成功后，当前节点才正式与之前的尾节点建立关联。 

 ![image-20220404174605431](img-深入理解AbstractQueuedSynchronizer(AQS)/image-20220404174605431.png)

同步队列遵循FIFO，首节点是获取同步状态成功的节点，首节点的线程在释放同步状态时，将会唤醒后继节点，而后继节点将会在获取同步状态成功时将自己设置为首节点，该过程如图所示：

 ![image-20220404174659118](img-深入理解AbstractQueuedSynchronizer(AQS)/image-20220404174659118.png)

如图，设置首节点是通过获取同步状态成功的线程来完成的，由于只有一个线程能够成功获取到同步状态，因此设置头节点的方法并不需要使用CAS来保证，它只需要将首节点设置成为原首节点的后继节点并断开原首节点的next引用即可。

现在我们可以清楚的知道这样几点：

1. **节点的数据结构，即AQS的静态内部类Node，节点的等待状态等信息**；
2. **同步队列是一个双向队列，AQS通过持有头尾指针管理同步队列**；
3. **节点的入队和出队实际上对应着锁的获取和释放两个操作：获取锁失败进行入队操作，获取锁成功进行出队操作。**

# 3、独占锁

## 独占锁的获取（acquire方法）

我们继续通过看源码和debug的方式来看，还是以上面的demo为例，调用lock()方法是获取独占式锁，获取失败就将当前线程加入同步队列，成功则线程执行。而lock()方法实际上会调用AQS的`acquire()`方法，源码如下

```java
public final void acquire(int arg) {
		//先看同步状态是否获取成功，如果成功则方法结束返回
		//若失败则先调用addWaiter()方法再调用acquireQueued()方法
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
}
```

关键信息请看注释，acquire根据当前获得同步状态成功与否做了两件事情：1. 成功，则方法结束返回，2. 失败，则先调用addWaiter()然后在调用acquireQueued()方法。

> **获取同步状态失败，入队操作**

当线程获取独占式锁失败后就会将当前线程加入同步队列，那么加入队列的方式是怎样的了？我们接下来就应该去研究一下addWaiter()和acquireQueued()。addWaiter()源码如下：

```java
private Node addWaiter(Node mode) {
    // 1. 将当前线程构建成Node类型
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    // 2. 当前尾节点是否为null？
    Node pred = tail;
    if (pred != null) {
        // 2.2 将当前节点尾插入的方式插入同步队列中
        node.prev = pred;
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 2.1. 当前同步队列尾节点为null，说明当前线程是第一个加入同步队列进行等待的线程
    enq(node);
    return node;
}
```

分析可以看上面的注释。程序的逻辑主要分为两个部分：

1. 当前同步队列的尾节点为null，调用方法enq()插入;
2. 当前队列的尾节点不为null，则采用尾插入（compareAndSetTail()方法）的方式入队。

另外还会有另外一个问题：如果 `if (compareAndSetTail(pred, node))`为false怎么办？会继续执行到enq()方法，同时很明显compareAndSetTail是一个CAS操作，通常来说如果CAS操作失败会继续自旋（死循环）进行重试。因此，经过我们这样的分析，enq()方法可能承担两个任务：

1. 处理当前同步队列尾节点为null时进行入队操作；
2.  如果CAS尾插入节点失败后负责自旋进行尝试。那么是不是真的就像我们分析的一样了？只有源码会告诉我们答案。

enq()源码如下：

```java
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) { // Must initialize
            //1. 构造头结点
            if (compareAndSetHead(new Node()))
                tail = head;
        } else {
            // 2. 尾插入，CAS操作失败自旋尝试
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

在上面的分析中我们可以看出在第1步中会先创建头结点，说明同步队列是**带头结点的链式存储结构**。带头结点与不带头结点相比，会在入队和出队的操作中获得更大的便捷性，因此同步队列选择了带头结点的链式存储结构。那么带头节点的队列初始化时机是什么？自然而然是在**tail为null时，即当前线程是第一次插入同步队列**。compareAndSetTail(t, node)方法会利用CAS操作设置尾节点，如果CAS操作失败会在`for (;;)`死循环中不断尝试，直至成功return返回为止。因此，对enq()方法可以做这样的总结：

1. **在当前线程是第一个加入同步队列时，调用compareAndSetHead(new Node())方法，完成链式队列的头结点的初始化**；
2. **自旋不断尝试CAS尾插入节点直至成功为止**。

现在我们已经很清楚获取独占式锁失败的线程包装成Node然后插入同步队列的过程了？那么紧接着会有下一个问题？在同步队列中的节点（线程）会做什么事情了来保证自己能够有机会获得独占式锁了？带着这样的问题我们就来看看acquireQueued()方法，从方法名就可以很清楚，这个方法的作用就是排队获取锁的过程，源码如下：

```java
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            // 1. 获得当前节点的先驱节点
            final Node p = node.predecessor();
            // 2. 当前节点能否获取独占式锁					
            // 2.1 如果当前节点的先驱节点是头结点并且成功获取同步状态，即可以获得独占式锁
            if (p == head && tryAcquire(arg)) {
                //队列头指针用指向当前节点
                setHead(node);
                //释放前驱节点
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 2.2 获取锁失败，线程进入等待状态等待获取独占式锁
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

程序逻辑通过注释已经标出，整体来看这是一个这又是一个自旋的过程（for (;;)），代码首先获取当前节点的先驱节点，**如果先驱节点是头结点的并且成功获得同步状态的时候（if (p == head && tryAcquire(arg))），当前节点所指向的线程能够获取锁**。反之，获取锁失败进入等待状态。整体示意图为下图：

 ![image-20220404181453451](img-深入理解AbstractQueuedSynchronizer(AQS)/image-20220404181453451.png)

> **获取锁成功，出队操作**

获取锁的节点出队的逻辑是：

```java
//队列头结点引用指向当前节点
setHead(node);
//释放前驱节点
p.next = null; // help GC
failed = false;
return interrupted;
```

setHead()方法为：

```java
private void setHead(Node node) {
    head = node;
    node.thread = null;
    node.prev = null;
}
```

将当前节点通过setHead()方法设置为队列的头结点，然后将之前的头结点的next域设置为null并且pre域也为null，即与队列断开，无任何引用方便GC时能够将内存进行回收。示意图如下：

 ![当前节点引用线程获取锁，当前节点设置为队列头结点.png](img-深入理解AbstractQueuedSynchronizer(AQS)/当前节点引用线程获取锁，当前节点设置为队列头结点.png)

那么当获取锁失败的时候会调用shouldParkAfterFailedAcquire()方法和parkAndCheckInterrupt()方法，看看他们做了什么事情。shouldParkAfterFailedAcquire()方法源码为：

```java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        /*
         * This node has already set status asking a release
         * to signal it, so it can safely park.
         */
        return true;
    if (ws > 0) {
        /*
         * Predecessor was cancelled. Skip over predecessors and
         * indicate retry.
         */
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        /*
         * waitStatus must be 0 or PROPAGATE.  Indicate that we
         * need a signal, but don't park yet.  Caller will need to
         * retry to make sure it cannot acquire before parking.
         */
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
```

shouldParkAfterFailedAcquire()方法主要逻辑是使用`compareAndSetWaitStatus(pred, ws, Node.SIGNAL)`使用CAS将节点状态由INITIAL设置成SIGNAL，表示当前线程阻塞。当compareAndSetWaitStatus设置失败则说明shouldParkAfterFailedAcquire方法返回false，然后会在acquireQueued()方法中for (;;)死循环中会继续重试，直至compareAndSetWaitStatus设置节点状态位为SIGNAL时shouldParkAfterFailedAcquire返回true时才会执行方法parkAndCheckInterrupt()方法，该方法的源码为：

```java
private final boolean parkAndCheckInterrupt() {
    //使得该线程阻塞
    LockSupport.park(this);
    return Thread.interrupted();
}
```

该方法的关键是会调用LookSupport.park()方法（关于LookSupport会在以后的文章进行讨论），该方法是用来阻塞当前线程的。因此到这里就应该清楚了，acquireQueued()在自旋过程中主要完成了两件事情：

1. **如果当前节点的前驱节点是头节点，并且能够获得同步状态的话，当前线程能够获得锁该方法执行结束退出**；
2. **获取锁失败的话，先将节点状态设置成SIGNAL，然后调用LookSupport.park方法使得当前线程阻塞**。

经过上面的分析，独占式锁的获取过程也就是acquire()方法的执行流程如下图所示：

 ![acquire()方法的执行流程](img-深入理解AbstractQueuedSynchronizer(AQS)/acquire()方法的执行流程.png)

