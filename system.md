# 进程、线程、协程

## 进程

表示一个程序的上下文执行活动（打开、执行、保存...）,是系统资源分配的最小单位,一个程序至少有一个进程，

## 线程

一个进程至少有一个线程。进程执行程序时候的最小调度单位（执行a，执行b...),是CPU调度的最小单位.进程相当于一个容器,而线程而是运行在容器里面的,因此对于容器内的东西,线程是共同享有的,因此线程间的通信可以直接通过全局变量进行通信,共享意味着竞争，导致数据不安全，为了保护内存空间的数据安全，引入"互斥锁"。

## 协程

协程是一种用户态的轻量级线程，协程的调度完全由用户控制

迭代器最基本的规定了对象可以通过next返回下一个值，而不是像数组，列表一样一次性返回。

生成器： 使用 yield 关键字的函数,生成器也可通过next返回下一个值。

<https://cloud.tencent.com/developer/article/1376478>

# Select、poll、epoll 

## select

各个客户端连接的文件描述符也就是套接字，都被放到了一个集合中，调用select函数之后会一直监视这些文件描述符中有哪些可读，如果有可读的描述符那么我们的工作进程就去读取资源.

## poll

poll 和 select 的实现非常类似，本质上的区别就是存放 fd 集合的数据结构不一样。select 在一个进程内可以维持最多 1024 个连接，poll 在此基础上做了加强，可以维持任意数量的连接。

但 select 和 poll 方式有一个很大的问题就是，我们不难看出来 select 是通过轮训的方式来查找是否可读或者可写，打个比方，如果同时有100万个连接都没有断开，而只有一个客户端发送了数据，所以这里它还是需要循环这么多次，造成资源浪费。

## epoll

epoll 是 select 和 poll 的增强版，epoll 同 poll 一样，文件描述符数量无限制。

epoll是基于内核的反射机制，在有活跃的 socket 时，系统会调用我们提前设置的回调函数。而 poll 和 select 都是遍历。

但是也并不是所有情况下 epoll 都比 select/poll 好，比如在如下场景：

在大多数客户端都很活跃的情况下，系统会把所有的回调函数都唤醒，所以会导致负载较高。既然要处理这么多的连接，那倒不如 select 遍历简单有效。

<https://baijiahao.baidu.com/s?id=1611547498841608701&wfr=spider&for=pc>

<https://www.jianshu.com/p/513d22383b9a>

# 进程间通信方式

## 无名管道

管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系

## 高级管道

将另一个程序当做一个新的进程在当前程序进程中启动，则它算是当前程序的子进程，这种方式我们成为高级管道方式

## 有名管道

有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信

## 消息队列

消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点

## 信号量

信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段

## 信号

信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生

## 共享内存

共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号两，配合使用，来实现进程间的同步和通信

## 套接字

套解字也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信

<https://www.cnblogs.com/zgq0/p/8780893.html>

