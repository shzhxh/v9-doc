### 计算机体系结构 
#### 目前的x处理器的ISA如下
1. 寄存器

   ra, rb, rc , rd, si, di, sp, bp, pc, flags, pdir

   | flags | 7    | 6    | 5    | 4    | 3    | 2    | 1    | 0         |
   | ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | --------- |
   | 标志位   | PG   | IF   | ZF   | SF   | OF   |      |      | USER/KERN |

   ​

2. 指令编码

   | 指令                | 机器码         | 字节数  |
   | ----------------- | ----------- | ---- |
   | halt              | 0x00        | 1    |
   | nop               | 0x10        | 1    |
   | rrmov %ra, %rb    | 0x20+rarb   | 2    |
   | irmov V, %rb      | 0x30+Frb+V  | 6    |
   | store %ra, D(%rb) | 0x40+rarb+D | 6    |
   | load D(%rb), %ra  | 0x50+rarb+D | 6    |
   | ALU %ra, %rb      | 0x6fn+rarb  | 2    |
   | jXX Dest          | 0x7fn+Dest  | 5    |
   | call Dest         | 0x80+Dest   | 5    |
   | ret               | 0x90        | 1    |
   | push %ra          | 0xa0+raF    | 2    |
   | pop %ra           | 0xb0+raF    | 2    |
   | in V, %ra         | 0xc0+Fra+V  | 3    |
   | out %ra, V        | 0xd0+raF+V  | 3    |

ALU里的fn对照表

| 0    | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| add  | sub  | and  | or   | xor  | shl  | shr  |

JXX里的fn对照表

| 0    | 1    | 2    | 3    | 4    | 5    | 6    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| jmp  | je   | jl   | jg   | jne  | jle  | jge  |



#### 为什么要这么设计ISA呢，理由如下

##### 寄存器

1. Y86的寄存器

   eax, ebx, ecx, edx, esi, edi, esp, ebp, 条件码ZF SF OF, 程序计数器PC

2. THINPAD的寄存器

   rx, ry, rz, 程序计数器PC, SP, 中断寄存器IH, 返回值寄存器RA, 标志寄存器T

3. V9处理器

   a, b, c, sp, 程序计数器pc, 状态寄存器flags, 页目录寄存器pdir, 浮点寄存器f, g

4. 比较以上3个处理器中的寄存器，考虑到较多的寄存器可以为编程提供方便，但x处理器不计划提供浮点运算，所以计划x处理器包含以下寄存器：

   ra, rb, rc , rd, si, di, sp, bp, pc, flags, pdir

##### 指令集 

1. 运算指令

   * Y86: addl, subl, andl, xorl

   * THINPAD: addiu, addiu3, addsp3, addsp, addu, and, cmp, cmpi, neg, not, or,  sll, sllv, slt, slti, sltu, sra, srav, srl, srlv, subu, xor

   * 比较以上两个处理器的指令，考虑到最简化的原则，所以计划x处理器实现以下指令：

     add, sub, and, or, not, xor, shl, shr

   * 发现非运算并不是必须的，可以用异或运算代替，故去掉not指令，计划实现如下指令：

     add, sub, and, or, xor, shl, shr

2. 跳转指令

   * Y86: jmp, jle, jl, je, jne, jge, jg, call, ret

   * THINPAD: b, beqz, bnez, bteqz, btnez, int, jalr, jr, jrra

   * 比较以上两个处理器的指令，计划x处理器实现以下指令：

     jmp, je, jl, jg, jne, jle, jge, call, ret, int

   * 考虑到int指令可以用call指令配合其它指令实现，故去掉int指令，计划实现如下指令：

     jmp, je, jl, jg, jne, jle, jge, call, ret

   * **为x处理器的标志寄存器设置了用户态和内核态切换的标志位，这是否足以实现内核态和用户态的切换呢？**
     为解决这个问题，我又重新学习了ucore_os_docs里关于保护模式的内容，以及ucore_lab1里内核态和用户态切换的代码。发现我一直以为段页式内存分配就是保护模式的全部内容，这种理解是错误的。段页式内存管理+用户态内核态切换才是保护模式的全部内容。段页式管理把内存从形式上区分开，用户态内核态把内存从权限上区分开。内核态用户态的区别还包括指令执行的权限，目前我只想到halt指令是不可以在用户态执行的。内核态用户态的切换最终依赖于中断。这需要考虑中断的问题。

   * **为x处理器的标志寄存器设置了中断标志位，这是否足以实现中断的功能呢？**
     我大概设想了一下中断功能的实现：1,通过rrmov和or指令打开中断标志位，2，使用push指令保存当前寄存器的值，3,使用call指令调用中断例程。我考虑这样操作中断是有很大问题的。1,是没有什么安全性可言，用户态的程序可以很容易地使用中断。2,是如果中断切换过程中如果被其它中断打断，这应该有比较大的问题。3,是call指令调用中断很容易和调用普通例程混淆，没有好的区分方法。还得再好好考虑一下中断的指令。x86处理器为什么要设置STI和CLI指令，我推测STI和CLI一定是特权指令，x86处理器并没有使用mov指令修改寄存器就是为了保护寄存器不被随意修改。我感觉在同一个寄存器内设置不同权限的标志位比较混乱，应该把拥有特权标志位的寄存器单独拿出来。

   * **实现内核态和用户态的切换最简单的方法是什么？其它处理器是怎么做的(MIPS,ARM,RISC V等)？**

     MIPS的做法和X86是类似的，只是所有中断都在0号中断里，系统调用在8号中断，其它都是异常。arm的做法和X86也是类似的，只是用了很多寄存器保存中断的各种状态。

3. 访存指令

   * Y86: mrmovl, rmmovl, pushl, popl

   * THINPAD: lw, lw_sp, sw, sw_rs, sw_sp

   * 比较以上两个处理器的指令，计划x处理器实现以下指令：

     load, store, push, pop

4. 系统指令

   * Y86: halt, nop
   * THINPAD: nop
   * x处理器计划实现: halt, nop

5. 寄存器传送指令

   * Y86: rrmovl, irmovl, cmovXX

   * THINPAD: li, mfih, mfpc, move, mtih, mtsp

   * 比较以上两个处理器的指令，计划x处理器实现以下指令：

     rrmov, irmov

6. 其它指令

   * Y86, THINPAD和V9均没有原子操作指令，但从ucore的学习可见原子操作指令是很重要的，需要考虑实现TS指令和swap指令的问题。
   * 以上指令是否可以实现内存管理，我目前还不太确定。
   * 以上指令是否可以实现外设管理，我目前还不太确定。


