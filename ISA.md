### 计算机体系结构 

#### 寄存器

1. Y86的寄存器

   eax, ebx, ecx, edx, esi, edi, esp, ebp, 条件码ZF SF OF, 程序计数器PC

2. THINPAD的寄存器

   rx, ry, rz, 程序计数器PC, SP, 中断寄存器IH, 返回值寄存器RA, 标志寄存器T

3. V9处理器

   a, b, c, sp, 程序计数器pc, 状态寄存器flags, 页目录寄存器pdir, 浮点寄存器f, g

4. 比较以上3个处理器中的寄存器，考虑到较多的寄存器可以为编程提供方便，但x处理器不计划提供浮点运算，所以计划x处理器包含以下寄存器：

   ra, rb, rc , rd, si, di, sp, bp, pc, flags, pdir

#### 指令集 

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

   * Y86, THINPAD和V9均没有原子操作指令，但从ucore的学习可见原子操作指令是很重要的，要不要实现TS指令和swap指令。
   * 以上指令是否可以实现内存管理，我目前还不太确定。
   * 以上指令是否可以实现外设管理，我目前还不太确定。

7. 指令与机器码对照表

   | flags | 7    | 6    | 5    | 4    | 3    | 2    | 1    | 0         |
   | ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | --------- |
   | 标志位   | PG   | IF   | ZF   | SF   | OF   |      |      | USER/KERN |

   ​

   | 指令              | 机器码         | 字节数  |
   | --------------- | ----------- | ---- |
   | halt            | 0x00        | 1    |
   | nop             | 0x10        | 1    |
   | rrmov ra, rb    | 0x20+rarb   | 2    |
   | irmov V, rb     | 0x30+Frb+V  | 6    |
   | store ra, D(rb) | 0x40+rarb+D | 6    |
   | load D(rb), ra  | 0x50+rarb+D | 6    |
   | ALU ra, rb      | 0x6fn+rarb  | 2    |
   | jXX Dest        | 0x7fn+Dest  | 5    |
   | call Dest       | 0x80+Dest   | 5    |
   | ret             | 0x90        | 1    |
   | push ra         | 0xa0+raF    | 2    |
   | pop ra          | 0xb0+raF    | 2    |

   ​
