### 模拟器

#### sim: CSAPP的模拟器

1. 数据结构

   * state_ptr：描述的是模拟器。pc程序计数器，r代表寄存器组的内存(mem_t)，m代表模拟器内存(mem_t)，cc记录的是状态码(第2位ZF，第1位SF，第0位OF)。

   * mem_t：描述的是内存，把内存看做是字节组成的数组。len数组长度，maxaddr最大地址，contents数组地址。

   * stat_t：描述的是模拟器的状态。STAT_BUB不懂, STAT_AOK就绪, STAT_HLT结束, STAT_ADR无效地址, STAT_INS无效指令, STAT_PIP流水。

   * 指令集：

     | instruction      | formal         | length(bytes) |
     | ---------------- | :------------- | ------------- |
     | nop              | 0x00(1byte)    | 1             |
     | halt             | 0x10(1byte)    | 1             |
     | rrmovl rA, rB    | 0x20+rArB      | 2             |
     | irmovl V, rB     | 0x30+8rB+V(4)  | 6             |
     | rmmovl rA, D(rB) | 0x40+rArB+D(4) | 6             |
     | mrmovl D(rB), rA | 0x50+rArB+D(4) | 6             |
     | OP1 rA, rB       | 0x6fn+rArB     | 2             |
     | jXX Dest         | 0x7fn+Dest     | 5             |
     | call Dest        | 0x80+Dest      | 5             |
     | ret              | 0x90           | 1             |
     | pushl rA         | 0xA0+rA8       | 2             |
     | popl rA          | 0xB0+0xrA8     | 2             |
     | iaddl V, rB      | 0xC0+0x8rB+V   | 6             |
     | leave            | 0xD0           | 1             |

     ​

2. 工作流程

   1. new_state函数初始化CPU的状态，由变量s表示
   2. fopen函数打开argv[1]参数指定的文件，由句柄code_file表示
   3. load_mem函数把code_file代表的文件复制到内存s->m处
   4. 循环调用step_state函数依次执行目标文件的指令(step_state函数执行单条指令，返回执行状态)
      1. 将s->m内存的位置在ftpc的字节赋值给byte0
      2. ftpc++
      3. byte0的高4位赋值为hi0，低4位赋值给lo0
      4. 通过need_regids判断指令是否需要寄存器，若需要则把当前ftpc内存中的值赋值给byte1，byte1的高4位再赋值给hi1，低4位再赋值给lo1， 且ftpc++
      5. 通过need_imm判断指令是否需要立即数，若需要则把当前ftpc内存中的值赋值给cval，且ftpc += 8
      6. 通过hi0判断是哪条指令：
         * I_NOP，空指令。什么都不做，只是将s->pc的值修改为当前ftpc的值。
         * I_HALT，退出指令。直接返回STAT_HLT状态。
         * I_RRMOVQ，在两个寄存器之间传递数据(包括有条件传递和无条件传递)。
         * I_IRMOVQ，将立即数传入寄存器。
         * I_RMMOVQ，将寄存器中的数据传入内存。
         * I_MRMOVQ，将内存中的数据传入寄存器。
         * I_ALU，ALU运算。
         * I_JMP，跳转指令。
         * I_CALL，函数调用。
         * I_RET，函数返回。
         * I_PUSHQ，压栈。
         * I_POPQ，出栈。
         * I_IADDQ，
         * default：打印出错误指令，返回STAT_INS指令。
   5. 打印模拟器执行前后的状态差异
   6. 释放模拟器占用的内存

#### em: v9的模拟器

1. v9模拟器与CSAPP的模拟器sim相比多了如下功能：时钟和键盘中断，TLB，用户态和内核态的划分，分页，中断管理
2. v9模拟器相比于sim更象操作系统，sim更接近于真实的CPU。