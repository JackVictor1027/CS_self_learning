# Mooc_os_lab1:bootloader的启动和简单OS

## **练习** **1**：理解通过 **make** **生成执行文件的过程。（要求在报告中写出对下述问题的回答）**

在此练习中，大家需要通过静态分析代码来了解：

### 1. 操作系统镜像文件 ucore.img 是如何一步一步生成的？(需要比较详细地解释 Makefile 中每一条相关命令和命令参数的含义，以及说明命令导致的结果)

首先，通过make "V="编译执行makefile文件，屏幕打印出的指令如下，我们挨个进行解释：

```shell
+ cc kern/init/init.c
gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
```
**指令1** ：这条指令使用 gcc 编译器编译 kern/init/init.c 文件，并生成目标文件 obj/kern/init/init.o。主要选项解释：
-Ikern/init/：指定头文件目录。
-fno-builtin：禁用内置函数。
-Wall：启用所有警告。
-ggdb：生成适用于 GDB 的调试信息。
-m32：生成32位代码。
-gstabs：生成 STABS 格式的调试信息。
-nostdinc：不包含标准头文件。
-fno-stack-protector：禁用栈保护。
-c：只编译，不链接。
-o obj/kern/init/init.o：输出目标文件。 

```
kern/init/init.c:95:1: warning: ‘lab1_switch_test’ defined but not used [-Wunused-function]
 lab1_switch_test(void) {
 ^
```
 **警告1**：有函数已经定义却没有被使用

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240729135749481.png" alt="image-20240729135749481" style="zoom:50%;" />

```
+ cc kern/libs/readline.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
```
**指令2**：和上面的指令作用是类似的，编译kern/libs/readline/c文件，并生成目标文件obj/kern/libs/readline.o。接下来，直到下一条解释前的所有指令的作用都是类似的，即初始化对应的OS功能模块。

```
+ cc kern/libs/stdio.c
gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
+ cc kern/debug/kdebug.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o
kern/debug/kdebug.c:251:1: warning: ‘read_eip’ defined but not used [-Wunused-function]
 read_eip(void) {
 ^
+ cc kern/debug/kmonitor.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o
+ cc kern/debug/panic.c
gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o
+ cc kern/driver/clock.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o
+ cc kern/driver/console.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o
+ cc kern/driver/intr.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o
+ cc kern/driver/picirq.c
gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o
+ cc kern/trap/trap.c
  gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o
  kern/trap/trap.c:14:13: warning: ‘print_ticks’ defined but not used [-Wunused-function]
   static void print_ticks() {
             ^
  kern/trap/trap.c:30:26: warning: ‘idt_pd’ defined but not used [-Wunused-variable]
   static struct pseudodesc idt_pd = {
                          ^
```

```
+ cc kern/trap/trapentry.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o
```
**指令3**：这条指令编译汇编文件 kern/trap/trapentry.S，生成目标文件 obj/kern/trap/trapentry.o。以下指令，有的根据汇编文件，有的根据C源文件编译产生目标文件.

```
+ cc kern/trap/vectors.S
gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o
+ cc kern/mm/pmm.c
gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o
+ cc libs/printfmt.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/printfmt.c -o obj/libs/printfmt.o
+ cc libs/string.c
gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/string.c -o obj/libs/string.o
```
```
+ ld bin/kernel
ld -m    elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/readline.o obj/kern/libs/stdio.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/debug/panic.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/intr.o obj/kern/driver/picirq.o obj/kern/trap/trap.o obj/kern/trap/trapentry.o obj/kern/trap/vectors.o obj/kern/mm/pmm.o  obj/libs/printfmt.o obj/libs/string.o
```
**指令4**：这条指令使用 ld 链接器将所有目标文件链接成内核文件 bin/kernel。主要选项解释：
-m elf_i386：生成 ELF 格式的 32 位目标文件。
-nostdlib：不链接标准库。
-T tools/kernel.ld：使用 tools/kernel.ld 链接脚本。
-o bin/kernel：输出文件 bin/kernel。

```
+ cc boot/bootasm.S
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o obj/boot/bootasm.o
+ cc boot/bootmain.c
gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o
+ cc tools/sign.c
gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o
gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
```
```
+ ld bin/bootblock
ld -m    elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
'obj/bootblock.out' size: 472 bytes
build 512 bytes boot sector: 'bin/bootblock' success!
```
**指令6**：这条指令将引导块目标文件链接成引导扇区文件 obj/bootblock.o。主要选项解释：
-N：将所有部分加载到内存中（主要用于引导扇区）。
-e start：入口函数为 start。
-Ttext 0x7C00：代码加载地址为 0x7C00。

```
dd if=/dev/zero of=bin/ucore.img count=10000
10000+0 records in
10000+0 records out
5120000 bytes (5.1 MB) copied, 0.0257016 s, 199 MB/s
```
**指令7**：这条指令使用 dd 工具创建一个大小为 5MB 的初始磁盘映像 bin/ucore.img，填充内容为零

```
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
1+0 records in
1+0 records out
512 bytes (512 B) copied, 0.000114097 s, 4.5 MB/s
```
**指令8**：这条指令将 bin/bootblock 写入磁盘映像 bin/ucore.img 的起始位置，不截断文件。

```
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
138+1 records in
138+1 records out
70775 bytes (71 kB) copied, 0.000239699 s, 295 MB/s
```
**指令9**：这条指令将 bin/kernel 写入磁盘映像 bin/ucore.img 的第二个扇区（即偏移量为 512 字节），不截断文件。


### 2.一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？
- 磁盘主引导扇区只有512字节
- 磁盘最后两个字节为0x55AA
- 由不超过466字节的启动代码和不超过64字节的硬盘分区表加上两个字节的结束符组成

补充材料：

如何调试 Makefile

当执行 make 时，一般只会显示输出，不会显示 make 到底执行了哪些命令。

如想了解 make 执行了哪些命令，可以执行：

$ make "V="

要获取更多有关 make 的信息，可上网查询，并请执行

$ man make

## 练习2：使用 qemu 执行并调试 lab1 中的软件。（要求在报告中简要写出练习过程）

为了熟悉使用 qemu 和 gdb 进行的调试工作，我们进行如下的小练习：
1. 从 CPU 加电后执行的第一条指令开始，单步跟踪 BIOS 的执行。
2. 在初始化位置 0x7c00 设置实地址断点,测试断点正常。
3. 在调用 qemu 时增加-d in_asm -D q.log 参数，便可以将运行的汇编指令保存在 q.log 中。
将执行的汇编代码与 bootasm.S 和 bootblock.asm 进行比较，看看二者是否一致。

> 一、用户态和内核态

简单来说内核态就是操作系统运行线程，用户态就是线程执行用户自己的程序。

- 用户态：

不能直接使用系统资源，也不能改变 CPU 的工作状态，并且只能访问这个用户程序自己的存储空间！
- 内核态：

系统中既有操作系统的程序，也有普通用户程序。为了安全性和稳定性，操作系统的程序不能随便访问，这就是内核态。即需要执行操作系统的程序就必须转换到内核态才能执行
内核态可以使用计算机所有的硬件资源

> 这段代码的含义是什么？
``` 
static void
lab1_print_cur_status(void) {
    static int round = 0; //表示该函数被调用的次数
    uint16_t reg1, reg2, reg3, reg4; //声明四个代表寄存器reg(0-3)的临时变量
    asm volatile (
            "mov %%cs, %0;"//这里的%n(n=1,2,3,......)，依据此段汇编代码的末尾的操作数约束，将寄存器的值存在相应的变量中
            "mov %%ds, %1;"
            "mov %%es, %2;"
            "mov %%ss, %3;"
            : "=m"(reg1), "=m"(reg2), "=m"(reg3), "=m"(reg4));//操作数约束
    cprintf("%d: @ring %d\n", round, reg1 & 3);//reg1 & 3 取 reg1 的最低两位，这表示当前的特权级别（ring level）。在 x86 架构中，有四个特权级别（0-3），最低两位定义了当前的特权级别。
    cprintf("%d:  cs = %x\n", round, reg1);
    cprintf("%d:  ds = %x\n", round, reg2);
    cprintf("%d:  es = %x\n", round, reg3);
    cprintf("%d:  ss = %x\n", round, reg4);
    round ++;
}
```

数字前加前缀 “％“，如％1，％2 等表示使用寄存器的样板操作数。可以使用的操作数总数取决于具体 CPU 中通用寄存器的数量，如 Intel 可以有 8 个。指令中有几个操作数，就说明有几个变量需要与寄存器结合，由 gcc 在编译时根据后面输出部分和输入部分的约束条件进行相应的处理。由于这些样板操作数的前缀使用了”％“，因此，在用到具体的寄存器时就在前面加两个“％”，如%%cr0。

![image-20240730204102565](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240730204102565.png)

## 练习3：分析bootloader加入保护模式的过程

BIOS 将通过读取硬盘主引导扇区到内存，并转跳到对应内存中的位置执行 bootloader。请分析bootloader 是如何完成从实模式进入保护模式的。

提示：需要阅读 3.2.1 小节“保护模式和分段机制”和 lab1/boot/bootasm.S 源码，了解如何从实模式切换到保护模式。

源码如下：

```
//引用头文件
#include <asm.h>

```
包含汇编相关的宏和定义。

```
//定义常量
# Start the CPU: switch to 32-bit protected mode, jump into C.
# The BIOS loads this code from the first sector of the hard disk into
# memory at physical address 0x7c00 and starts executing in real mode
# with %cs=0 %ip=7c00.

.set PROT_MODE_CSEG,        0x8                     # kernel code segment selector
.set PROT_MODE_DSEG,        0x10                    # kernel data segment selector
.set CR0_PE_ON,             0x1                     # protected mode enable flag

```
- 定义了保护模式下代码段和数据段的**选择子**。
- 定义了CR0寄存器中用于**开启保护模式**的标志位。

```
# start address should be 0:7c00, in real mode, the beginning address of the running bootloader
.globl start
start:
.code16                                             # Assemble for 16-bit mode
    cli                                             # Disable interrupts
    cld                                             # String operations increment

```
- start 是程序入口，使用 .globl 声明为全局符号。
- .code16 指定接下来的代码是16位模式。
- cli 禁用中断，cld 设置字符串操作的方向标志位。

```
//设置段寄存器
    # Set up the important data segment registers (DS, ES, SS).
    xorw %ax, %ax                                   # Segment number zero
    movw %ax, %ds                                   # -> Data Segment
    movw %ax, %es                                   # -> Extra Segment
    movw %ax, %ss                                   # -> Stack Segment
```
将 ax 清零，并将其加载到数据段（ds）、额外段（es）和堆栈段（ss）寄存器中。

```
//启用A20地址线
    # Enable A20:
    #  For backwards compatibility with the earliest PCs, physical
    #  address line 20 is tied low, so that addresses higher than
    #  1MB wrap around to zero by default. This code undoes this.
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.1

    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port

seta20.2:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.2

    movb $0xdf, %al                                 # 0xdf -> port 0x60
    outb %al, $0x60                                 # 0xdf = 11011111, means set P2's A20 bit(the 1 bit) to 1
    
```
- 使能A20地址线，允许访问1MB以上内存。
- 使用键盘控制器（8042）来设置A20地址线。

```
//切换到保护模式
    # Switch from real to protected mode, using a bootstrap GDT
    # and segment translation that makes virtual addresses
    # identical to physical addresses, so that the
    # effective memory map does not change during the switch.
    lgdt gdtdesc
    movl %cr0, %eax
    orl $CR0_PE_ON, %eax
    movl %eax, %cr0

    # Jump to next instruction, but in 32-bit code segment.
    # Switches processor into 32-bit mode.
    ljmp $PROT_MODE_CSEG, $protcseg
```
- 加载全局描述符表（GDT），并设置CR0寄存器的保护模式启用位。
- 使用长跳转指令（ljmp）切换到32位代码段。
  

```
.code32                                             # Assemble for 32-bit mode
protcseg:
    # Set up the protected-mode data segment registers
    movw $PROT_MODE_DSEG, %ax                       # Our data segment selector
    movw %ax, %ds                                   # -> DS: Data Segment
    movw %ax, %es                                   # -> ES: Extra Segment
    movw %ax, %fs                                   # -> FS
    movw %ax, %gs                                   # -> GS
    movw %ax, %ss                                   # -> SS: Stack Segment

    # Set up the stack pointer and call into C. The stack region is from 0--start(0x7c00)
    movl $0x0, %ebp
    movl $start, %esp
    call bootmain

    # If bootmain returns (it shouldn't), loop.
spin:
    jmp spin
```
- 切换到32位模式后，设置数据段、额外段、FS、GS和堆栈段寄存器。
- 设置堆栈指针，并调用C函数 bootmain。
- 若C代码返回，进入无限循环。

```
//GDT和描述符表
# Bootstrap GDT
.p2align 2                                          # force 4 byte alignment
gdt:
    SEG_NULLASM                                     # null seg
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)           # code seg for bootloader and kernel
    SEG_ASM(STA_W, 0x0, 0xffffffff)                 # data seg for bootloader and kernel

gdtdesc:
    .word 0x17                                      # sizeof(gdt) - 1
    .long gdt                                       # address gdt
```
- 定义了GDT（全局描述符表），包含空段、代码段和数据段描述符。
- gdtdesc 是GDT的描述符，指明GDT的大小和地址。

## 练习4：**分析** **bootloader** **加载** **ELF** **格式的** **OS** **的过程。（要求在报告中写出分析）**

### 1.什么是ELF

ELF(Executable and linking format)文件格式是 Linux 系统下的一种常用目标文件(object file)格式，有三种主要类型: 

 用于执行的可执行文件(executable file)，用于提供程序的进程映像，加载的内存执行。 这也是本实验的 OS 文件类型。

 用于连接的可重定位文件(relocatable file)，可与其它目标文件一起创建可执行文件和共享目标文件。

 共享目标文件(shared object file),连接器可将它与其它可重定位文件和共享目标文件连接成其它的目标文件，动态连接器又可将它与可执行文件和其它共享目标文件结合起来创建一个进程映像。

### 2. bootmain.c源码分析

首先我们可以知道的是，bootmain这个程序就是bootloader的入口程序。

1. 先读取磁盘的第一页，大小为512字节*8。这里执行的ELF文件就是executable file，用于提供程序的进程映像、加载的内存执行。

2. 如果验证发现，这里一个合法的ELF（ELF得到执行之后，可以映像到虚拟文件），就接着执行第3步，否则直接跳到第5步.

   > ELF本质上就是一种数据结构，用于在程序执行时提供必要信息。

3. 加载当前页的每一个程序段。

4. 执行ELF header程序

   > program header 描述与程序执行直接相关的目标文件结构信息，用来在文件中定位各个段的映像，同时包含其他一些用来为程序创建进程映像所必需的信息。

5. 引导程序发出错误警告

   > 这里调用了两次主要执行I/O端口读写的outw(port,value)函数，port指的是目标端口地址，value是要写入端口的16位的值。在这里的作用是通过调用，硬件或固件可以检测到引导加载程序进入了错误状态，并采取适当的措施（如重启或进入故障处理模式）。

### 3.bootloader 如何读取硬盘扇区的？

bootloader 让 CPU 进入**保护模式**后，下一步的工作就是**从硬盘上加载并运行 OS**。考虑到实现的简单性，bootloader 的访问硬盘都是 **LBA 模式的 PIO（Program IO）**方式，即所有的 IO 操作是**通过CPU 访问硬盘的 IO 地址寄存器**完成。

总的来说，bootloader读取硬盘扇区大致可以分为四步：

1. 等待磁盘准备好
2. 发出读取扇区的命令
3. 等待磁盘准备好
4. 把磁盘扇区数据读到指定内存

![image-20240801141405966](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240801141405966.png)

### 4. bootmain.c文件首部注释翻译

源码文件种首段部分有对源码的功能说明，感觉很有用，于是就顺势翻译了下。

```c
/* *********************************************************************
 * This a dirt simple boot loader, whose sole job is to boot
 * an ELF kernel image from the first IDE hard disk.
 * 这是一个简单的bootloader，它的功能就是从首个IDE硬盘区加载ELF内核镜像
 *
 * DISK LAYOUT
 *  * This program(bootasm.S and bootmain.c) is the bootloader.
 *    It should be stored in the first sector of the disk.
 *	本程序就是bootloader，它应该存储在第一个磁盘区。
 *
 *  * The 2nd sector onward holds the kernel image.
 * 第二个磁盘区应该防止内核镜像
 *
 *  * The kernel image must be in ELF format.
 * 其中，内核镜像必须依赖于ELF
 *
 * BOOT UP STEPS
 * 启动步骤
 *  * when the CPU boots it loads the BIOS into memory and executes it
 * 当 CPU 启动时，它会将 BIOS 载入内存并执行它 
 *
 *  * the BIOS intializes devices, sets of the interrupt routines, and
 *    reads the first sector of the boot device(e.g., hard-drive)
 *    into memory and jumps to it.
 * BIOS 会初始化设备、设置中断例程，并将启动设备（如硬盘）的第一个扇区读入内存并跳转到该扇区。并跳转到它。
 *
 *  * Assuming this boot loader is stored in the first sector of the
 *    hard-drive, this code takes over...
 * 假设这个bootloader存储在硬盘，这份代码就会接管系统
 *
 *  * control starts in bootasm.S -- which sets up protected mode,
 *    and a stack so C code then run, then calls bootmain()
 * 控制bootasm.S中的开始，这将会开始保护模式，并设置一个C代码可以运行的栈，再调用bootmain函数
 *
 *  * bootmain() in this file takes over, reads in the kernel and jumps to it.
 * 在本文件中的bootmain程序开始了工作，读取内核并跳到它所在的程序当中去。
 * */
```

## **练习** **5**：实现函数调用堆栈跟踪函数 （需要编程）

提示：可阅读 3.3.1 小节“函数堆栈”，了解编译器如何建立函数调用关系的。在完成 lab1 编译后，

- 查看lab1/obj/bootblock.asm，了解 bootloader 源码与机器码的语句和地址等的对应关系；
- 查看 lab1/obj/kernel.asm，了解ucore OS 源码与机器码的语句和地址等的对应关系

### 何为函数堆栈？

栈是一个很重要的编程概念（编译课和程序设计课都讲过相关内容），与编译器和编程语言有紧密的联系。理解调用栈最重要的两点是：**栈的结构，EBP 寄存器的作用**。一个函数调用动作可分解为：

1. 零到多个 PUSH 指令（用于参数入栈）

2. 一个 CALL 指令。CALL 指令内部其实还暗含了一个将返回地址（即 CALL 指令下一条指令的地址）压栈的动作（由硬件完成）。

3. 几乎所有本地编译器都会在每个函数体之前插入类似如下的汇编指令：

   >  pushl %ebp 
   >
   >  movl %esp , %ebp 

这样在程序执行到一个函数的实际指令前，已经有以下数据顺序入栈：参数、返回地址、ebp寄存器。由此得到类似如下的栈结构（参数入栈顺序跟调用方式有关，这里以 C 语言默认的CDECL 为例）：

+| (栈底方向 | 高位地址

| .................... |

| .................... |

| 参数 3 |

| 参数 2 |

| 参数 1 |

| 返回地址 |

| 上一层[ebp] | <-------- [ebp]

|局部变量 | 低位地址

图 4 函数调用栈结构

这两条汇编指令的含义是：**首先将 ebp 寄存器入栈，然后将栈顶指针 esp 赋值给 ebp**。“mov ebp esp”这条指令表面上看是用 esp 覆盖 ebp 原来的值，其实不然。因为给 ebp 赋值之前，原 ebp 值已经被压栈（位于栈顶），而新的 ebp 又恰恰指向栈顶。**此时 ebp 寄存器就已经处于一个非常重要的地位——该寄存器中存储着栈中的一个地址（原 ebp 入栈后的栈顶），从该地址为基准，向上（栈底方向）能获取返回地址、参数值，向下（栈顶方向）能获取函数局部变量值，而该地址处又存储着上一层函数调用时的 ebp 值。**

一般而言，**ss:[ebp+4]处为返回地址，ss:[ebp+8]处为第一个参数值**（最后一个入栈的参数值，此处假设其占用 4 字节内存），ss:[ebp-4]处为第一个局部变量，ss:[ebp]处为上一层 ebp 值。**由于 ebp中的地址处总是“上一层函数调用时的 ebp 值”，而在每一层函数调用中，都能通过当时的 ebp 值“向上（栈底方向）”能获取返回地址、参数值，“向下（栈顶方向）”能获取函数局部变量值**。如此形成递归，直至到达栈底。这就是函数调用栈。

提示：练习 5 的正确实现取决于对这一小节的正确理解和掌握。

## 练习6：**完善中断初始化和处理 （需要编程）**

请完成编码工作和回答如下问题：

1. 中断向量表中一个表项占多少字节？其中哪几位代表中断处理代码的入口？

> 当 CPU 收到中断（通过 8259A 完成，有关 8259A 的信息请看附录 A）或者异常的事件时，它会暂停执行当前的程序或任务，通过**一定的机制**跳转到**负责处理这个信号的相关处理例程中**，在完成对这个事件的处理后再跳回到刚才被打断的程序或任务中。中断向量和中断服务例程的对应关系主要是由 IDT（中断描述符表）负责。**操作系统在 IDT 中设置好各种中断向量对应的中断描述符，留待 CPU 在产生中断后查询对应中断服务例程的起始地址**。而 IDT 本身的起始地址保存在 idtr 寄存器中。
>
> 中断描述符表把每个中断或异常编号和一个指向中断服务例程的描述符联系起来。同 GDT 一样，**IDT 是一个 8 字节的描述符数组**，但 IDT 的第一项可以包含一个描述符。CPU 把中断（异常）号乘以 8 做为 IDT 的索引。**IDT 可以位于内存的任意位置，CPU 通过 IDT 寄存器（IDTR）的内容来寻址 IDT 的起始地址。**

2. 请编程完善 kern/trap/trap.c 中对中断向量表进行初始化的函数 idt_init。在 idt_init 函数中，依次对所有中断入口进行初始化。使用 mmu.h 中的 SETGATE 宏，填充 idt 数组内容。注意除了系统调用中断(T_SYSCALL)以外，其它中断均使用中断门描述符，权限为内核态权限；而系统调用中断使用异常，权限为陷阱门描述符。每个中断的入口由tools/vectors.c 生成，使用 trap.c 中声明的 vectors 数组即可。

3. 请编程完善 trap.c 中的中断处理函数 trap，在对时钟中断进行处理的部分填写 trap 函数中处理时钟中断的部分，使操作系统每遇到 100 次时钟中断后，调用 print_ticks 子程序，向屏幕上打印一行文字”100 ticks”。

完成这问题 2 和 3 要求的部分代码后，运行整个系统，可以看到大约每 1 秒会输出一次”100 ticks”，而按下的键也会在屏幕上显示。

提示：可阅读 3.3.2 小节“中断与异常”。
