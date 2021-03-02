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

## 2.2进程的管理

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
    + Blocked， Suspend→ Blocked：当 Blocked， Suspend队列中有一个进程的  阻塞事件可能会很快发生，则可将一个Blocked， Suspend进程换入内存，变Blocked
    + Running→ Ready， Suspend：当执行进程的时间片用完时，会转换为Ready。或，一个高优先级的 Blocked，Suspend进程正好变为非阻塞状态，Os可以将执行进程转换为Ready， Suspend状态
    + A→Exit：通常， Running→Exit。但某些OS中，父进程可以终止其子进程，使任何状态的进程都可转换为退出状态

#### 2.2.2 进程的控制

+ **内存表（Memory Tables）**：
+ **I/O Tables**：
  + 设备状态是空闲可用的还是已分配的
  + I/O设备状态信息
  + I/O传输的源和目的地
+ **文件表（File Tables）**：
  + 存在的文件
  + 文件的外存的位置
  + 文件的状态
  + 文件的属性
  + 文件管理系统
+ **进程表（Process Tbale）**
  + 进程的位置
  + 进程的属性
    + 进程Id
    + 进程状态
    + 进程在内存的位置
  + 包含一组程序
    + 局部和全局变量的数据位置
    + 常量
    + 堆栈
  + Process control block（PCB）
    + 属性的集合（所有进程的属性）
    + 进程映像（Process image）
      + 把一个进程逻辑上的所有信息合起来叫进程映像
    + 是OS控制和管理进程时所用的基本数据结构
    + PCB是相关进程存在于系统中的唯一标志；系统根据PCB而感知相关进程的存在
    + PCB组成：
      + 进程标识符（Identifiers）
        + 进程ID
        + 父进程ID
        + 用户ID
      + 处理器状态信息
        + 用户可见寄存器（User-Visible Registers）
        + 控制与状态寄存器（Control and Status Registers）
          + PC寄存器（Program Counter）
          + 条件寄存器（Condition codes ）
          + 堆栈指针（Stack Pointers）
          + 进程调度的状态信息（Scheduling and State Informatica）
            + Process state
            + Priority
            + Scheduling-related Informatica
            + Event
          + 数据结构
          + 进程间通信（Interprocess Communication）
          + 内存管理（Memory Management）
            + 内存是分段还是分页来划分的
            + 资源的占用和空闲信息
  + 操作系统内核功能（Typical Function of and OS Kernel）
    + 资源管理功能：
      + Process Management：进程创建和终止、调度、状态转换、同步和通信、管理PCB
      + Memory Management：为进程分配地址空间、对换、段/页管理
      + I/O Management：缓存管理、为进程分配I/O通道和设备
    + 支撑功能
      + 中断处理（Interrupt handing）
      + 时钟管理（Timing）
      + 原语（Primitive）:最小的程序操作，不可中断的操作
      + 统计（Accounting）
      + 监测（Monitoring）

####  2.2.3 进程的转换 

+ 进程**原语**：
  + **Process Switch**：进程切换
    
    + 导致进程切换的操作：
      + Clock interrupt：process has executed for the maximum allowable time slice（时间片）
      + I/O interrupt
      + Memory fault（存储访问失效，比如说一个数据要去外存拿）
      + Trap（陷阱）
      + Supervisor call（管理程序介入）
      
+ 进程切换的操作过程
    
  1、**Save context** of process including program counter and other registers
    
  2、Update the PCB of the process that is currently running
    
  3、Move PCB to appropriate（挪用） queue-ready，blocked
    
  4、Select another process for execution
    
  5、update the PCB of the process selected
    
  6、Update memory-management data structures
    
  7、**Restore context** of the selected process
    
+ 进程切换VS模式切换
    
      + Process Switch：是作用于进程之间的种操作。当分派程序收回当前进程的CPU并准备把它分派给某个就绪进程时，该操作将被引用
  +  Mode switch：是进程内部所引用的一种操作。当进程映像所包含的程序引用核心子系统所提供的系统调用时，该操作将被引用
    
+ **Create and Terminate**：创建和终止 
  
  + 进程创建步骤
  
      1、为进程分配一个唯一标识号ID：主进程表中增加一个新的表项
      2、为进程分配空间：用户地址空间、用户栈空间、PCB空问。若共享已有空间，则应建立相应的链接。
      3、初始化PCB：进程标识、处理机状态信息、进程状态
      4、建立链接：若调度队列是链表，则将新进程插入到就绪或（就绪，挂起）链表
    5、建立或扩展其他数据结构
  
  + 进程终止的步骤
  
      1、根据被终止进程的标识符ID，找到其PCB，读出该进程的状态；
      2、若该进程为执行状态，则终止其执行，调度新进程执行；
      3、若该进程有子孙进程，则立即终止其所有子孙进程
      4、将该进程的全部资源，或归还给其父进程，或归还给系统
    5、.将被终止进程（的PCB）从所在的队列中移出，等待其它程序来搜集信息
  
+ **Block and Wakeup**：阻塞和唤醒
  
    + 阻塞原语block（）
      + 概述：当出现阻塞事件，进程调用阻塞原语将自己阻塞。状态变为“阻塞状态”，并进入相应事件的阻塞队列
    + 唤醒原语wakeup（）
    + 概述：当阻塞进程期待的事件发生，有关进程调用唤醒原语，将等待该事件的进程唤醒。状态变为Ready，插入就绪队列
  
+ **Suspend and Activate**：挂起与激活（基于交换技术）
  
  + 挂起原语 suspend
  
    当出现挂起事件，系统利用挂起原语将指定进程或阻塞状态进程挂起。进程从内存换到外存，状态改变： Ready→Ready， Suspend；Blocked→ Blocked， Suspend，插入相应队列
  
    + 激活原语 active
       当激活事件发生，系统利用激活原语将指定进程激活。进程从外存换入到内存，状态改变：Ready， Suspend → Ready；Blocked， Suspend → Blocked，插入相应队列

####  2.2.4 线程 

 降低多进程切换和调度开销、提高并发度

进程是资源分配单位，线程是调度单位，

  ##### 2.2.4.1 线程描述

+ An execution state(running, ready, etc.)
+ Saved thread context when not running
+ Has an execution stack
+ Some per-thread static storage for local variables
+ Access to the memory and resources of its process。all threads of a process share this

  ##### 2.2.4.2 线程的好处

+ Takes less time to create a new thread than a process.
+ Less time to terminate a thread than a process
+ Less time to switch between two threads within the same process
+ Since threads within the same process share memory and files. they can communicate with each other without invoking the kernel.
+ Suspending a process involves suspending all threads of the process since all threads share the same address space
+ Termination of a process, terminates all threads within the process