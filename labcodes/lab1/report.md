# Lab1 实验报告

## 练习1: 理解通过make生成执行文件的过程

### 操作系统镜像文件ucore.img是如何一步一步生成的？

```
+ cc kern/init/init.c
gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
```
编译源文件init.c为目标文件init.o, 用到了以下参数:
- `-fno-builtin`: 只有以`__builtin_`作为前缀的函数才会被认为是内建函数, 避免gcc进行优化导致部分函数变为built-in进而导致丢失调试信息无法调试.
- `-Wall`: 打开一组特定的warning开关, 有助于发现更多的warning.
- `-ggdb`: 产生gdb可用的调试信息.
- `-m32`: 产生适用于32位机的代码.
- `-gstabs`: 规定产生的gdb调试信息为stabs格式.
- `-nostdinc`: 不在系统路径中搜索头文件, 只在当前目录和`-I`参数列出的目录中搜索.
- `-fno-stack-protector`: 禁用stack protector, 方便和栈有关的调试和修改(例如挑战实验中通过对栈的修改实现状态切换).
- `-I`: 指定头文件搜索目录.
- `-c`: 仅编译不链接.
- `-o`: 指定输出文件.

```
+ cc kern/libs/stdio.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
+ cc kern/libs/readline.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
+ cc kern/debug/panic.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o
+ cc kern/debug/kdebug.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o
+ cc kern/debug/kmonitor.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o
+ cc kern/driver/clock.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o
+ cc kern/driver/console.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o
+ cc kern/driver/picirq.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o
+ cc kern/driver/intr.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o
+ cc kern/trap/trap.c
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o
+ cc kern/trap/vectors.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o
+ cc kern/trap/trapentry.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o
+ cc kern/mm/pmm.c
gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o
+ cc libs/string.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/string.c -o obj/libs/string.o
+ cc libs/printfmt.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/printfmt.c -o obj/libs/printfmt.o
```
与编译init.c时用到的命令和参数相同.

```
+ ld bin/kernel
ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/stdio.o obj/kern/libs/readline.o obj/kern/debug/panic.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/picirq.o obj/kern/driver/intr.o obj/kern/trap/trap.o obj/kern/trap/vectors.o obj/kern/trap/trapentry.o obj/kern/mm/pmm.o  obj/libs/string.o obj/libs/printfmt.o
```
将之前编译得到的目标文件链接为kernel文件, 用到了以下参数:
- `-m elf_i386`: 指定链接产物为在i386机器上运行的elf文件.
- `-nostdlib`: 仅在命令中列出的库目录中搜索(不包括指定的ld脚本中列出的目录).
- `-T`: 指定ld脚本.
- `-o`: 指定输出文件.

```
+ cc boot/bootasm.S
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o obj/boot/bootasm.o
+ cc boot/bootmain.c
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o
```
编译bootloader部分, 用到了以下参数:
- `-Os`: 以减小生成文件体积为目标进行编译优化.
- 其余参数上文已经提及.

```
+ cc tools/sign.c
gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o
gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
```
编译用于生成合格的主引导扇区的工具.

```
+ ld bin/bootblock
ld -m    elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
```
将bootloader部分链接为bootblock文件, 用到的参数如下:
- `-N`: 代码段和数据段可读可写.
- `-e`: 指定程序入口.
- `-Ttext`: 指定代码段的起始地址为0x7C00.
- 其余参数上文已经提及.

```
'obj/bootblock.out' size: 488 bytes
build 512 bytes boot sector: 'bin/bootblock' success!
```
执行辅助程序sign, 在生成的bootblock末尾加上0x55AA, 使之能被识别.

```
dd if=/dev/zero of=bin/ucore.img count=10000
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
```
使用dd命令将bootblock和kernel拼接为一个文件ucore.img.
- 生成一个10000*512Byte的空文件.
- 从头开始写入bootblock, 占据开头的512个Byte.
- 跳过开头的512Byte, 写入kernel.

主要参数如下:
- `if`: 指定输入文件.
- `of`: 指定输出文件.
- `count=N`: 仅copy前N个ibs(1个ibs默认大小为512Byte).
- `conv=notrunc`: 拷贝完毕后不对文件进行截断.
- `seek=N`: 跳过前N个obs(1个obs默认大小为512Byte).

对于kernel模块, 首先编译各源文件, 然后进行链接得到kernel, 用同样方法得到bootblock. 最后使用dd命令将bootblock和kernel拼接起来得到ucore.img.

### 一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？

主引导扇区大小为512Byte, 且结束标志字为0x55AA.

## 练习2: 使用qemu执行并调试lab1中的软件

### 从CPU加电后执行的第一条指令开始, 单步跟踪BIOS的执行

1. 修改Makefile使之读取gdbinit的配置. 将Makefile中debug设置内的最后一行改为`$(V)$(TERMINAL) -e "gdb -q -tui -x tools/gdbinit"`.

2. 修改gdbinit文件为:
```
set architecture i8086
target remote: 1234
```

3. 执行`make debug`, 此时gdb会暂停在加电后的第一条指令, CS=0xf, IP=0xfff0. 使用`si`指令可以进行单步调试.

### 在初始化位置0x7c00设置实地址断点, 测试断点正常

1. 修改gdbinit文件为:
```
set architecture i8086
target remote :1234
break *0x7c00
c
```

2. 执行`make debug`, 此时gdb会暂停在0x7c00处.

### 从0x7c00开始跟踪代码运行, 将单步跟踪反汇编得到的代码与bootasm.S和bootblock.asm进行比较
执行`make debug`, 使用`set arch i386`, 然后使用`x /40i $pc`反汇编当前指令, 得到调用bootmain前的部分如下.
```
=> 0x7c00:      cli
   0x7c01:      cld
   0x7c02:      xor    %eax,%eax
   0x7c04:      mov    %eax,%ds
   0x7c06:      mov    %eax,%es
   0x7c08:      mov    %eax,%ss
   0x7c0a:      in     $0x64,%al
   0x7c0c:      test   $0x2,%al
   0x7c0e:      jne    0x7c0a
   0x7c10:      mov    $0xd1,%al
   0x7c12:      out    %al,$0x64
   0x7c14:      in     $0x64,%al
   0x7c16:      test   $0x2,%al
   0x7c18:      jne    0x7c14
   0x7c1a:      mov    $0xdf,%al
   0x7c1c:      out    %al,$0x60
   0x7c1e:      lgdtl  (%esi)
   0x7c21:      insb   (%dx),%es:(%edi)
   0x7c22:      jl     0x7c33
   0x7c24:      and    %al,%al
   0x7c26:      or     $0x1,%ax
   0x7c2a:      mov    %eax,%cr0
   0x7c2d:      ljmp   $0xb866,$0x87c32
   0x7c34:      adc    %al,(%eax)
   0x7c36:      mov    %eax,%ds
   0x7c38:      mov    %eax,%es
   0x7c3a:      mov    %eax,%fs
   0x7c3c:      mov    %eax,%gs
   0x7c3e:      mov    %eax,%ss
   0x7c40:      mov    $0x0,%ebp
   0x7c45:      mov    $0x7c00,%esp
   0x7c4a:      call   0x7d0d
```
与bootasm.S和bootblock.asm相比, 多了一条`adc    %al,(%eax)`指令.

### 自己找一个bootloader或内核中的代码位置, 设置断点并进行测试
1. 修改gdbinit文件为:
```
file bin/kernel
target remote :1234
break kern_init
continue
```

2. 执行`make debug`, gdb会停在kern_init函数的入口处.

## 练习3: 分析bootloader进入保护模式的过程
从实模式进入保护模式的代码在`bootasm.S`中. 需要进行开启A20, 初始化GDT表和使能并进入保护模式的操作.

### 开启A20
```
seta20.1:
    inb $0x64, %al                                  # 从0x64读取状态, 直到端口空闲后开始写入
    testb $0x2, %al
    jnz seta20.1

    movb $0xd1, %al                                 # 向0x64写入控制码0xd1, 意为接下来要修改P2端口的内容
    outb %al, $0x64                                 

seta20.2:
    inb $0x64, %al                                  # 从0x64读取状态, 直到端口空闲后开始写入
    testb $0x2, %al
    jnz seta20.2

    movb $0xdf, %al                                 # 向0x60端口写入0xdf, 则P2端口的内容变为0xdf
    outb %al, $0x60                                 # 根据手册P2端口的P21用于控制A20, 0xdf对应位为1, 表明A20已被开启
```

### 初始化GDT
```
lgdt gdtdesc                                        # 从gdtdesc载入gdt
```
gdtdesc设置如下:
```
# Bootstrap GDT
.p2align 2                                          
gdt:
    SEG_NULLASM                                     # 空段, 可能是为了避免与实模式下的地址冲突?
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)           # bootloader用到的代码段
    SEG_ASM(STA_W, 0x0, 0xffffffff)                 # bootloader用到的数据段
```
这里设置的gdt将代码段和数据段扩展到全部地址空间, 使得这里的物理地址和虚拟地址相同, 避免了切换到保护模式后内存映射失效.

### 使能和进入保护模式
```
    # 对CR0寄存器的CR0_PE_ON置位, 进入保护模式
    movl %cr0, %eax
    orl $CR0_PE_ON, %eax
    movl %eax, %cr0

    # 跳转的同时更改CS, 保护模式下CS作为段选择子, 故需要通过ljmp将其修改为代码段的段选择子0x8
    ljmp $PROT_MODE_CSEG, $protcseg
```

## 练习4: 分析bootloader加载ELF格式的OS的过程

### bootloader如何读取硬盘扇区
读取函数为`void readseg(uintptr_t va, uint32_t count, uint32_t offset)`. 从offset开始将count个bytes读取到va开始的内存处.

该函数调用`void readsect(void *dst, uint32_t secno)`. 将第secno个扇区的内容读取到dst开始的内存处, 每个扇区大小为512Byte. 读取过程如下:

- 调用`waitdisk()`等待. 从0x1F7端口读取寄存器&0xC0, 结果为0x40时表示可以进行读取.
- 向0x1F2端口写入1, 0x1F3~0x1F6按照小端序写入扇区号, 0x1F7写入0x20.
- `waitdisk()`等待读取.
- 调用`insl`指令读取. 这里读取单位为double word, 所以需要把SECTSIZE/4.

### bootloader如何加载ELF格式的OS
- 首先使用`readseg`读取ELF文件头部, 文件头部构成elfhdr结构体.
- 检查是否为合法的ELF文件.
- 读取每个program segment.
- 从头部得到entry并调用, 进入操作系统.

## 练习5: 实现函数调用堆栈跟踪函数
需要完成`void print_stackframe()`函数.

- 调用`read_ebp()`得到ebp(当前函数栈底), 调用`read_eip()`得到eip(当前函数返回地址).
- 函数参数的起始存放地址为`(uint32_t*)(ebp) + 2`.
- 调用`print_debuginfo(eip-1)`打印调试信息.
- 上层函数的eip存放于`((uint32_t*)(ebp) + 1)`.
- 上层函数的ebp存放于`(uint32_t*)(ebp)`. 由此可沿着函数调用栈逐层上溯, 结束标志为层数达到调用栈最大深度或者上层的ebp为0.

最后一行对应函数`void bootmain(void)`的调用, 返回地址为0x7d68. 栈底位于0x7c00, 故函数的ebp为0x7c00-0x4(返回地址)-0x4(原ebp)=0x7bf8.

## 练习6: 完善中断初始化和处理

### 中断描述符表（也可简称为保护模式下的中断向量表）中一个表项占多少字节？其中哪几位代表中断处理代码的入口？
参见`mmu.h`中gatedesc结构体的定义, 一个表项占64字节. 其中0~15位和48~63位分别代表中断处理代码入口地址的低16位和高16位.

### 请编程完善kern/trap/trap.c中对中断向量表进行初始化的函数idt_init
具体实现见代码. 主要步骤如下:
- 将中断向量表的每一项都进行初始化, 使用中断门描述符, 段选择子为GD\_KTEXT, 特权级为DPL\_KERNEL.
- 系统调用中断(T_SYSCALL)使用陷阱门描述符且权限为DPL\_USER.
- 使用lidt指令加载中断向量表.

### 请编程完善trap.c中的中断处理函数trap, 在对时钟中断进行处理的部分填写trap函数中处理时钟中断的部分
具体实现见代码. 只需修改case语句中的`IRQ\_OFFSET+IRQ\_TIMER`分支, 每次处理均进行++ticks, 当遇到TICK\_NUM次后打印信息.