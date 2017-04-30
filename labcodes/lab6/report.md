# LAB6 实验报告

## 练习0: 填写已有实验

需要进行改动的地方:

- proc初始化时初始化LAB6的相关变量, 包括`rq`, `run_link`和`time_slice`.
- 时钟中断的处理: 每次时钟中断都要调用`sched_proc_tick`函数(调度器的接口), 若每个`TICK_NUM`才调用一次则会因为时间片过大导致练习2的结果不准确. `sched_proc_tick`接口需要自己在`sched.c`和`sched.h`内添加以绕开`static`关键字的限制.