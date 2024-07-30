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

## 使用 qemu 执行并调试 lab1 中的软件。（要求在报告中简要写出练习过程）

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

## 分析bootloader加入保护模式的过程

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
