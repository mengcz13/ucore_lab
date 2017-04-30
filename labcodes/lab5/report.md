# LAB5 实验报告

## 练习1: 加载应用程序并执行

### 实现过程

需要实现的部分是`load_icode`函数中构造用户态进程trapframe的过程. 该过程需要把trapframe的cs, ds, es, ss, esp设置为用户态的对应部分, 与LAB4的内核线程类似, 只不过将内核态换成了用户态. trapframe的eip应设置为前面读取到的用户程序的入口, 同时eflags需要把使能中断位置位以在从trapframe恢复后开中断.

### 用户态进程从被选择到执行第一条指令的过程

- `schedule`函数从当前进程队列中选择第一个`PROC_RUNNABLE`的进程作为要执行的下一个进程.
- `proc_run`函数修改tss的esp0, 修改cr3, 执行`switch_to`开始进程切换.
- `switch_to`函数保存前一个进程的执行现场, 恢复下一个进程的执行现场, 最后跳转到下一个进程. 若下一个进程是创建后第一次执行, 则跳转位置是`forkret`函数(在创建用户进程的函数中已经设置好), 否则跳转位置在之前切换时已经被保存.
- `forkret`函数实际上是调用`forkrets`函数, 将运行栈切换到构造好的trapframe栈顶, 然后执行`__trapret`从构造的trapframe中返回.
- `__trapret`函数就是一般的从中断返回的过程, 最后的iret使得程序跳转到trapframe中设置的eip. 这里的eip在`load_icode`中已经被设置为用户进程的入口, 故跳转后用户进程即开始执行第一条指令.

## 练习2: 父进程复制自己的内存空间给子进程

### 实现过程

需要实现的部分是`copy_range`函数中拷贝进程内存空间的部分. 首先要确定源地址和目的地址, 分别由父进程/子进程的pde和当前虚地址确定. 循环每次copy1页大小的内存, 使用`memcpy`函数即可, 最后把目的地址对应的pte填充上正确的物理地址和标志位. 循环直到start和end之间的所有空间均被拷贝为止.

### "Copy on Write" 机制的概要实现

fork时将原本需要复制的内存进行标记, 子进程只复制父进程的页表. 父/子进程需要执行写操作时根据标志位进行判断, 若标志位表明该内存与父进程共用, 则取消标志并复制该部分到新的内存空间.

## 练习3: 理解进程执行fork/exec/wait/exit的实现, 以及系统调用的实现

### 进程执行fork/exec/wait/exit的实现

用户态的`sys_fork/sys_exec/sys_wait/sys_exit`均调用`syscall`执行int系统调用, 区别在于参数和系统调用号不同. 内核态`syscall`函数会根据trapframe传入的调用号和参数执行指定的内核态中实现系统调用的函数`sys_fork/sys_exec/sys_wait/sys_exit`.

### 系统调用的实现

- `sys_fork`: 执行`do_fork`函数, 参数为父进程的trapframe指针和系统调用前的esp. `do_fork`函数完成了子进程创建, 内存空间复制, 加入进程队列, 向父进程返回pid的步骤.
- `sys_exec`: 执行`do_execve`函数, 从指定的elf文件读取新进程内容并覆盖当前进程.
- `sys_wait`: 执行`do_wait`函数, 用于父进程等待子进程的退出. 当等待的子进程进入僵尸状态之后, 父进程将其从队列中删除并清理其内存空间, 否则父进程执行`schedule`让子进程执行.
- `sys_exit`: 执行`do_exit`函数, 清理内存空间并唤醒下一个进程.

### fork/exec/wait/exit在实现中是如何影响进程的执行状态的?

- fork: 新产生的进程进入`PROC_RUNNABLE`状态.
- exec: 当前进程为`PROC_RUNNABLE`状态但是内容被填充.
- wait: 若确实需要等待, 则当前进程进入`PROC_SLEEPING`状态, 否则状态不变继续执行.
- exit: 当前进程进入`PROC_ZOMBIE`状态, 等待父进程回收.

### ucore中一个用户态进程的执行状态生命周期图
|
|`alloc_proc`
V
`PROC_UNINIT`
|
|`sys_fork`, `proc_init`
V
`PROC_RUNNABLE` --`sys_wait`, `try_free_pages`, `do_sleep`--> `PROC_SLEEPING`
|         ^                                                        |
|         |                                                        |
|         -------- `wakeup_proc`------------------------------------
|
|`sys_exit`
V
`PROC_ZOMBIE`