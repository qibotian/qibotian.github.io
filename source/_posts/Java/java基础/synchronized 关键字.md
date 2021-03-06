---
title: synchronized 关键字
date: 2019-08-01
categories: ['JAVA']
tags: ['synchronized']
comments: true
id: javasynchronized
---


<!--more-->


# 1. synchronized 关键字的作用

* 修饰实例方法

* 修饰静态方法

* 修饰代码块


# 2. synchronized 原理

* 修饰同步语句块

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; synchronized 同步语句块是使用monitorenter和monitorexit指令。 当执行到 monitorenter指令时，线程会尝试获取锁或者monitor(每个对象头都存有monitor对象)。

* 修饰方法

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; synchronized 修饰的方法会有一个ACC_SYNCHRONIZED标识，该标识会表名这个方式是个同步方法。

# 3. JDK1.6 对 synchronized 的优化

JDK1.6 主要引入了偏向锁，轻量级锁，自旋锁，适应性自旋锁，锁消除，锁粗话等操作来减少锁操作的开销。

锁主要存在四个状态，依次是 无锁，偏向锁，轻量级锁，重量级锁。他们和随着竞争的升级而逐渐升级，但是不能降级。

* 偏向锁  

    线程1访问代码块并获取锁对象时，会在java对象头和栈帧记录中记录偏向的锁的ThreadID, 因为偏向锁不会主动释放，所以当线程1再次获取锁时，需要比较当前线程的ID是否和对象头中的ID一致，如果一致则无需用CAS来获取锁。
    如果不一致（说明其他线程占有了偏向锁）则需要查看对象头中记录的线程1是否还存活，如果没有存活，则锁对象将被重置为无锁状态，可以竞争将其设置为偏向锁；如果存活，那么立刻查找该线程的栈帧信息，如果还是需要继续持
    有这个对象的锁，那么暂停线程1，撤销偏向锁，升级为轻量锁，如果该线程不再使用锁，那么将锁对象状态设为无所状态，重新设置为偏向锁。

    偏向锁会偏向第一次获得他的线程，如果在接下来的执行过程中，该锁没有被其他线程获取，那么持有偏向锁的线程就不会同步。如果所竞争比较激烈，那么偏向锁就会失效，升级为轻量级锁。
* 轻量级锁  

    线程1在获取欧对象锁时，会将java对象头MarkWord复制一份到线程1的帧栈中（1），然后使用CAS把对象头中内容替换为线程1的锁记录的地址（2）。
    如果在（1）操作之后（2）操作之前，线程2也打算获取获取锁，在线程2把第（1）步操作做好后，准备CAS将对象头内容替换为自己的锁记录地址，发现线程1已经把对象头换了，那么线程2就尝试使用自旋锁来等待线程1释放锁。

    轻量级锁主要使用CAS

* 自旋锁  

    如果持有锁的线程在很短的时间内能够释放资源，那么那些等待锁资源的线程就不需要做内核态和用户态之间切换进入阻塞挂起状态，他只需要等一等（自旋），等待持有锁的线程释放锁后可立即获取锁，这样就避免用户线程和内核切换的消耗。

# 4. synchronized 和 ReentranLock的区别

* 两者都属于可重入锁
* synchronized 依赖于JVM实现， ReentranLock依赖于API实现
* 相比于 synchronized ,ReentranLock 的可以实现更加高级的功能，比如公平锁，等待可中断，可实现选择性通知（Condition）
