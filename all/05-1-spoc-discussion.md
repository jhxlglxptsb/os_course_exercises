#lec11 进程／线程概念spoc练习

## 视频相关思考题

### 11.1 进程的概念

1. 什么是程序？什么是进程？
    - 程序：对实现预期目的一系列动作的执行过程的描述，由一系列操作指令的序列组成
    - 进程：具有一定独立功能的程序在一个数据集合上的一次动态执行过程

2. 进程有哪些组成部分？
    - 程序
    - 数据
    - 执行状态

3. 请举例说明进程的独立性和制约性的含义。
    - 独立性指每个进程的工作不相互影响
    - 制约性指访问共享数据/资源或进程间的同步而产生制约


4. 程序和进程联系和区别是什么？
    - 联系
        - 进程是操作系统处于执行状态程序的抽象
        - 同一个程序的多次执行过程对应为不同的进程
        - 进程执行需要资源有：内存，CPU
    - 区别
        - 进程是动态的，程序是静态的
        - 进程是暂时的，程序是永久的
        - 进程的组成包括程序、数据和进程控制块


### 11.2 进程控制块

1. 进程控制块的功能是什么？
    - 进程创建
    - 进程终止
    - 进程的组织管理

2. 进程控制块中包括什么信息？
    - 进程的标识信息
    - 处理机现场
    - 进程控制信息

3. ucore的进展控制块数据结构定义中哪些字段？有什么作用？
    ```
    struct proc_struct {
        enum proc_state state;                      // Process state
        int pid;                                    // Process ID
        int runs;                                   // the running times of Proces
        uintptr_t kstack;                           // Process kernel stack
        volatile bool need_resched;                 // bool value: need to be rescheduled to release CPU?
        struct proc_struct *parent;                 // the parent process
        struct mm_struct *mm;                       // Process's memory management field
        struct context context;                     // Switch here to run process
        struct trapframe *tf;                       // Trap frame for current interrupt
        uintptr_t cr3;                              // CR3 register: the base addr of Page Directroy Table(PDT)
        uint32_t flags;                             // Process flag
        char name[PROC_NAME_LEN + 1];               // Process name
        list_entry_t list_link;                     // Process link list 
        list_entry_t hash_link;                     // Process hash list
    };
    ```

  
### 11.3 进程状态

1. 进程生命周期中的相关事件有些什么？它们对应的进程状态变化是什么？
    - 进程创建：创建->就绪
    - 进程执行：运行
    - 进程等待：等待
    - 进程抢占
    - 进程唤醒
    - 进程结束：退出

### 11.4 三状态进程模型

1. 运行、就绪和等待三种状态的含义？7个状态转换事件的触发条件是什么？
    - 运行：占用CPU执行指令
    - 就绪：已获取所有其他资源，等待CPU资源
    - 等待：等待某事件，处理暂停状态
    - 转换事件触发条件：启动、进入就绪队列、被调度、等待事件、时间片用完、事件发生、结束

### 11.5 挂起进程模型

1. 引入挂起状态的目的是什么？
    - 减少占用内存

2. 引入挂起状态后，状态转换事件和触发条件有什么变化？
    - 加入了就绪挂起和等待挂起状态
    - 通过激活从就绪挂起到就绪，从等待挂起到等待

3. 内存中的什么内容放到外存中，就算是挂起状态？
    - 进程内核栈

### 11.6 线程的概念

1. 引入线程的目的是什么？
    - 在同一地址空间内实现并发的函数执行

2. 什么是线程？
    - 线程是进程的一部分，描述指令流执行状态，它是进程中的指令执行流的最小单元，是CPU调度的的基本单位

3. 进程与线程的联系和区别是什么？
    - 进程是资源分配单位，线程是CPU调度单位 
    - 进程拥有一个完整的资源平台，而线程只独享指令流执行的必要资源 
    - 线程能减少并发执行的时间和空间开销
 
### 11.7 用户线程

1. 什么是用户线程？
    - 在用户空间实现的线程

2. 用户线程的线程控制块保存在用户地址空间还是在内核地址空间？
    - 用户地址空间


### 11.8 内核线程

1. 用户线程与内核线程的区别是什么？
    - 用户线程由一组用户级的线程库函数来完成线程的管理 
    - 内核线程通过系统调用实现线程

2. 同一进程内的不同线程可以共用一个相同的内核栈吗？
    - 用户线程可以，内核线程不行

3. 同一进程内的不同线程可以共用一个相同的用户栈吗？
    - 不能

