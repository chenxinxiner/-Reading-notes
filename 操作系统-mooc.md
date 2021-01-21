# 操作系统

## 1 操作系统概述

### 1.1 什么是操作系统

#### 1.1.1 关于操作系统的四种基本观点：

+ User/Computer Interface：该观点认为OS是计算机用户使用计算机系统的接口，它为计算机用户提供了方便的工作环境
+ Virtual Machine：该观点认为OS是建立在计算机硬件平台上的虚拟机器，它为应用软件提供了许多比计算机硬件功能更强或计算机硬件所没有的功能
+ **Resource Manager**：该观点认为OS是计算机系统中各类资源的管理者，它负责分配、回收以及控制系统中的各种硬件资源
+ Job Organizer：该观点认为OS是计算机系统工作流程的组织者，它负责协调在系统中运行的各个应用软件的运行次序

#### 1.1.2 0S的功能性需求

+ 计算机用户需要的用户命令

  由OS实现的所有用户命令所构成的集合常被人们称为OS的 Interface（用户接口）；有时也称为命令接口。

+ 应用软件需要的 System Call（系统调用）由OS实现的所有系统调用所构成的集合被人们称为程序接口或应用编程接口（ Application Programming Interface， API）

说明：System Call（系统调用）:指由OS实现的应用软件在运行过程中可以引用的 System Service。

当前两种常用的API： POSIX.1、WIN32API

# 2 进程

## 2.1 概述

+ 进程是程序在一个数据集合上的运行过程，是系统进行资源分配和调度的一个独立单位
+ 进程的结构：Programs（程序）、Datas（数据）、PCB（Process Control Block）

## 2.2进程的控制

#### 2.2.1 进程的状态

##### 2.2.1.1 五状态描述

+ Running（执行）：占用处理机（单处理机环境中，某一时刻仅一个进程占用处理机），进入Running一定是通过调度

+ Ready（就绪）：准备执行，排队队列

+ Blocked（阻塞）：**等待某事件发生** 才能执行，如等待I/O完成等

+ New（新状态）：进程已经创建，但未被OS接纳为可执行进程

+ Exit（退出）：因停止或取消，被OS从执行状态释放，有些系统叫僵死状态

  状态之间的转换：

  Null→ New：新创建进程首先处于新状态	

  New→ Ready：OS接纳新状态进程为就绪进程

  Ready→ Running：OS只能从就绪进程中选一个进程执行

  Running→Exit：执行状态的进程执行完毕，或被取消，则转换为退出状态

  Running→ Ready：分时系统中，时间片用完，或优先级高的进程到来，将终止优先级低的进程的执行

  Running→ Blocked：执行进程需要等待某事件发生通常因进程需要的系统调用不能立即完成，而阻塞

  Blocked→ Ready：当阻塞进程等待的事件发生，就转换为就绪状态

  Ready→Exit：某些系统允许父进程在任何情况下终止其子进程。若一个父进程终止，其子孙进程都必须终止

  Blocked -> Exit：同前

##### 2.2.1.2 七状态描述

+ **Suspend（挂起）**：进程从内存转到外存（如：磁盘），基于**交换技术**，也就是一个I/O过程
+ 特征如下：
    + 不能立即执行
    + 可能是等待某事件发生。若是：则阻塞条件**独立**于挂起条件，即使阻塞时间发生，该进程也不能执行（也就是说即使阻塞时间发生了，该进程还是挂起状态）。
    + 使之挂起的进程为：自身、其父进程、OS
    + 只有挂起她得进程才能使之由挂起状态转换为其他状态
  + 四种状态组合
    + Ready：进程在内存，准备执行
    + Blocked：进程在内存，等待事件
    + Ready， Suspend：进程在外存，只要调入内存即可执行
    + Blocked， Suspend：进程在外存，等待事件
  + 跟挂起相关的状态转换：
    + · Blocked→ Blocked，Suspend：Os通常将阻塞进程换出，
       以腾出内存空间
    + Blocked，Suspend→ Ready，Suspend：当 Blocked， Suspend进程等待的事件发生时，可以将其转换为 Ready， Suspend
    +  Read， Suspend→ Ready：Os需要调入一个进程执行时
    + Ready→ Ready， Suspend：一般，OS挂起阻塞进程。但有时也会挂起就绪进程，释放足够的内存空问
    + New→ Ready， Suspend（New→ Ready）：新进程创建后，可以插入到 Ready队列或 Ready， Suspend队列。若无足够的内存分配给新进程，则需要 New → Ready， Suspend
    + Blocked， Suspend→ Blocked：当 Blocked， Suspend队列中有一个进程的阻塞事件可能会很快发生，则可将一个Blocked， Suspend进程换入内存，变Blocked
    + Running→ Ready， Suspend：当执行进程的时间片用完时，会转换为Ready。或，一个高优先级的 Blocked，Suspend进程正好变为非阻塞状态，Os可以将执行进程转换为Ready， Suspend状态
    + A→Exit：通常， Running→Exit。但某些OS中，父进程可以终止其子进程，使任何状态的进程都可转换为退出状态

#### 2.2.1 进程的控制

 