## RISC-V ISA手册

本手册分为两卷，上卷讲用户级指令，下卷讲特权级指令。

### RISC-V 手册上卷(riscv-spec-v2.2)

1. 介绍

   设计目标。不使用商业ISA的原因。不使用OpenRISC的原因。

   1. RISC-V ISA概述

      共有3种类型的指令：基本指令，标准扩展指令和特殊扩展指令。基本指令被命名为I，是不可更改，必须被包含的指令，它们都是整数指令。标准扩展指令包括M(扩展乘除指令)，A(扩展原子指令)，F(单精度浮点指令扩展)，D(双精度浮点指令扩展)，G(IMAFD五种指令的总和，提供了完整的通用标量指令集，是默认工具链支持的指令集)。特殊扩展指令只用于特定领域，不适用于全部通用领域。

   2. 指令长度编码

      基本指令固定是32位的，然而扩展指令可以是变长的，但必须是16的倍数。当低2位不是11时指令长度为16位，当低2位是11时指令长度为32位，当低5位为11111时指令长48位，当低6位为111111时指令长64位，当低7位全1则要计算[14:12]位的数才能得出指令长度。

      基本指令集是小端系统，但扩展指令集即可做成小端也可做成大端。

   3. 异常、陷阱和中断

      异常是指令错误引起的，陷阱是线程自主调用的，中断是外部事件引起的。

2. RV32I指令集

   RV32I是基本指令集，它被设计足以支持一个现代的操作系统，而且除了A扩展外它基本上能模拟所有的其它扩展。RV32I共计47条指令。

   1. 程序员模型

      用户可见的寄存器有3种：1个恒为0的x0，31个通用寄存器x1-x31，pc。

   2. 基本指令格式

      在基本ISA里，所有指令都是32位且必须4字节对齐。共有四种基本格式：R-type是寄存器-寄存器操作，I-type是寄存器-立即数操作，S-type，U-type。

      立即数字段里放的是立即数的位置，而不是它本身。

      R-type: **funct7**[31:25] **rs2**[24:20] **rs1**[19:15] **funct3**[14:12] **rd**[11:7] **opcode**[6:0]

      I-type: **imm[11:0]**[31:20] **rs1**[19:15] **funct3**[14:12] **rd**[11:7] **opcode**[6:0]

      S-type: **imm[11:5]**[31:25] **rs2**[24:20] **rs1**[19:15] **funct3**[14:12] **imm[4:0]**[11:7] **opcode**[6:0]

      U-type: **imm[31:12]**[31:12] **rd**[11:7] **opcode**[6:0]

   3. 立即数编码的变体

      B是S的变体，J是U的变体

      B-type: **imm[12]imm[10:5]**[31:25] **rs2**[24:20] **rs1**[19:15] **funct3**[14:12] **imm[4:1]imm[11]**[11:7] **opcode**[6:0]

      J-type:**imm[20]imm[10:1]imm[11]imm[19:12]**[31:12] **rd**[11:7] **opcode**[6:0]

   4. 整数计算指令(共计22条指令)

      | imm+rs1+funct3+rd+opcode                 | funct3 | opcode | 意义                              |
      | ---------------------------------------- | ------ | ------ | ------------------------------- |
      | ADDI rd, rs1, imm                        | ADDI   | OP-IMM | rd = rs1 + imm                  |
      | SLTI rd, rs1, imm (set less than immediate) | SLTI   | OP-IMM | rd = (rs1<rs2) ? 1 : 0(用于有符号数)  |
      | SLTIU rd, rs1, imm                       | SLTIU  | OP-IMM | rd = (rs1<rs2) ? 1 : 0(用于无符号数)  |
      | ANDI rd, rs1, imm                        | ANDI   | OP-IMM | rd = rs1 & imm                  |
      | ORI rd, rs1, imm                         | ORI    | OP-IMM | rd = rs1 \| imm                 |
      | XORI rd, rs1, imm                        | XORI   | OP-IMM | rd = rs1 ^ imm                  |
      | SLLI rd, rs1, imm                        | SLLI   | OP-IMM | rd = rs1 << imm[4:0]            |
      | SRLI rd, rs1, imm                        | SRLI   | OP-IMM | rd = (unsigned) rs1 >> imm[4:0] |
      | SRAI rd, rs1, imm                        | SRAI   | OP-IMM | rd = (signed) rs1 >> imm[4:0]   |

      | imm+rd+opcode                            | opcode | 意义                    |
      | ---------------------------------------- | ------ | --------------------- |
      | LUI rd, imm (load upper immediate)       | LUI    | rd = (imm << 12)      |
      | AUIPC rd, imm (add upper immediate to pc) | AUIPC  | rd = (imm << 12) + PC |

      | funct7+rs2+rs1+funct3+rd+opcode | funct7  | funct3 | opcode | 意义                                  |
      | ------------------------------- | ------- | ------ | ------ | ----------------------------------- |
      | ADD rd, rs1, rs2                | 0000000 | ADD    | OP     | rd = rs1 + rs2                      |
      | SLT rd, rs1, rs2                | 0000000 | STL    | OP     | rd = (rs1<rs2) ? 1 : 0 (用于有符号数)     |
      | SLTU rd, rs1, rs2               | 0000000 | STLU   | OP     | rd = (rs1<rs2) ? 1 : 0 (用于无符号数)     |
      | AND rd, rs1, rs2                | 0000000 | AND    | OP     | rd = rs1 & rs2                      |
      | OR rd, rs1, rs2                 | 0000000 | OR     | OP     | rd = rs1 \| rs2                     |
      | XOR rd, rs1, rs2                | 0000000 | XOR    | OP     | rd = rs1 ^ rs2                      |
      | SLL rd, rs1, rs2                | 0000000 | SLL    | OP     | rd = rs1 << (rs2 & 0x1F )           |
      | SRL rd, rs1, rs2                | 0000000 | SRL    | OP     | rd = (unsigned) rs1 >> (rs2 & 0x1F) |
      | SUB rd, rs1, rs2                | 0100000 | SUB    | OP     | **待查证**                             |
      | SRA rd, rs1, rs2                | 0100000 | SRA    | OP     | rd = (signed) rs1 >> (rs2 & 0x1F)   |

      NOP指令的编码：ADDI x0, x0, 0

   5. 控制转移指令(8条指令)

      - 无条件跳转(U-type or J-type)

        | 指令                                       | 编码                                | 意义                           |
        | ---------------------------------------- | --------------------------------- | ---------------------------- |
        | JAL rd, imm(jump and link)               | imm+rd+JAL(opcode)                | rd = pc + 4; pc = imm << 12; |
        | JALR rd, rs1, imm(jump and link register) | imm+rs1+0(funct3)+rd+JALR(opcode) | rd = pc + 4; pc = rs1 + imm; |

        ​

      - 条件分支(S-type or B-type)

        | imm+rs2+rs1+funct3+imm+opcode | funct3   | opcode | 意义           |
        | ----------------------------- | -------- | ------ | ------------ |
        | BEQ                           | BEQ      | BRANCH | if(rs1==rs2) |
        | BNE                           | BNE      | BRANCH | if(rs1!=rs2) |
        | BLT/BLTU                      | BLT/BLTU | BRANCH | if(rs1<rs2)  |
        | BGE/BGEU                      | BGE/BGEU | BRANCH | if(rs1>=rs2) |

        ​

   6. LOAD和STORE指令(8条指令)

      | imm+rs1+funct3+rd+opcode | funct3 | opcode | 意义              |
      | ------------------------ | ------ | ------ | --------------- |
      | LW(32位)                  | 宽度     | LOAD   | rd=mem(rs1+imm) |
      | LH(16位，符号扩展)             | 宽度     | LOAD   | rd=mem(rs1+imm) |
      | LHU(16位，无符号扩展)           | 宽度     | LOAD   | rd=mem(rs1+imm) |
      | LB(8位，符号扩展)              | 宽度     | LOAD   | rd=mem(rs1+imm) |
      | LBU(8位，无符号扩展)            | 宽度     | LOAD   | rd=mem(rs1+imm) |

      | imm+rs2+rs1+funct3+imm+opcode | funct3 | opcode | 意义               |
      | ----------------------------- | ------ | ------ | ---------------- |
      | SW(32位)                       | 宽度     | STORE  | mem(rs1+imm)=rs2 |
      | SH(16位)                       | 宽度     | STORE  | mem(rs1+imm)=rs2 |
      | SB(8位)                        | 宽度     | STORE  | mem(rs1+imm)=rs2 |

      ​

   7. 内存模型(2条指令)

      我理解内存模型说的是IO设备也当作内存来使用。

      | imm[11:0]                         | rs1     | funct3  | rd      | opcode   | 意义        |
      | --------------------------------- | ------- | ------- | ------- | -------- | --------- |
      | imm[11:8]保留，imm[7:4]前续，imm[3:0]后续 | 保留，应置为0 | FENCE   | 保留，应置为0 | MISC-MEM | 顺序化IORW访问 |
      | imm[11:0]保留，应置为0                  | 保留，应置为0 | FENCE.I | 保留，应置为0 | MISC-MEM | 同步指令和数据流  |

      ​

   8. 控制和状态寄存器指令(6条指令)

      - CSR指令

      | csr         | rs1       | funct3 | rd   | opcode | 意义                      |
      | ----------- | --------- | ------ | ---- | ------ | ----------------------- |
      | source/dest | source    | CSRRW  | dest | SYSTEM | rd=csr; csr=rs1         |
      | source/dest | source    | CSRRS  | dest | SYSTEM | rd=csr; csr\|=rs1       |
      | source/dest | source    | CSRRC  | dest | SYSTEM | rd=csr; csr &= !rs1     |
      | source/dest | uimm[4:0] | CSRRWI | dest | SYSTEM | rd=csr; csr=uimm[4:0]   |
      | source/dest | uimm[4:0] | CSRRSI | dest | SYSTEM | rd=csr; csr\|=uimm[4:0] |
      | source/dest | uimm[4:0] | CSRRCI | dest | SYSTEM | rd=csr; csr\|=uimm[4:0] |

      - 计时器和计数器(伪指令)

      | csr                    | rs1  | funct3 | rd   | opcode | 意义            |
      | ---------------------- | ---- | ------ | ---- | ------ | ------------- |
      | RDCYCLE/RDCYCLEH       | 0    | CSRRS  | dest | SYSTEM | 读取cycle CSR   |
      | RDTIME/RDTIMEH         | 0    | CSRRS  | dest | SYSTEM | 读取time CSR    |
      | RDINSTRENT/RDINSTRENTH | 0    | CSRRS  | dest | SYSTEM | 读取instret CSR |

      ​

   9. 环境调用和断点(1条指令)

      | funct12 | rs1  | funct3 | rd   | opcode | 意义           |
      | ------- | ---- | ------ | ---- | ------ | ------------ |
      | ECALL   | 0    | PRIV   | 0    | SYSTEM | 用于产生对执行环境的请求 |
      | EBREAK  | 0    | PRIV   | 0    | SYSTEM | 调试器用它返回调试环境  |

      ​

3. RV32E：RV32I的精简版，为嵌入式系统而设计

   1. 程序员模型：通用寄存器从31个(x1~x31)减少到15个(x1~x15)
   2. 指令集：与RV32I相同，但计时器和计数器伪指令不是必须的。
   3. 扩展：可进行M,A,C扩展，只有两种特权级(用户模式和机器模式)

4. RV64I：RV32I的变体，只描述与RV32I的不同之处。

   1. 寄存器状态：寄存器扩展为64位，且支持64位用户地址空间。

   2. 整数计算指令(增加了9条特有指令)

      RV64I的指令长度也是32位的，操作的是64位数值。但也提供了变种指令来操作32位数值，这些指令的操作码后面都增加了后缀“W"，是RV64I特有的指令。

      | imm       | rs1  | funct3 | rd   | opcode    |
      | --------- | ---- | ------ | ---- | --------- |
      | imm[11:0] | src  | ADDIW  | dest | OP-IMM-32 |
      | imm[4:0]  | src  | SLLIW  | dest | OP-IMM-32 |
      | imm[4:0]  | src  | SRLIW  | dest | OP-IMM-32 |
      | imm[4:0]  | src  | SRAIW  | dest | OP-IMM-32 |

      | funct7  | rs2  | rs1  | funct3    | rd   | opcode |
      | ------- | ---- | ---- | --------- | ---- | ------ |
      | 0000000 | src2 | src1 | ADDW      | dest | OP-32  |
      | 0000000 | src2 | src1 | SLLW/SRLW | dest | OP-32  |
      | 0100000 | src2 | src1 | SUBW/SRAW | dest | OP-32  |

      ​

   3. Load和Store指令(增加了3条指令)

      增加LD(64位)，LWU(32位无符号)，SD(64位）三条指令。

   4. 系统指令：与RV32I完全相同，只是操作的对象换成了64位的。

5. RV128I

6. M：整数乘除扩展

7. A：原子扩展

8. F：浮点扩展

9. D：双精度扩展

10. Q：四精度扩展

11. L：十进制浮点扩展

12. C：压缩指令扩展

13. B：位操作扩展

14. J：动态翻译语言扩展

15. T：事物内存扩展

16. P：封装的单指令多数据流扩展

17. V：向量操作扩展

18. N：用户级中断扩展

19. G：RV32/64G 指令集列表

20. RISC-V汇编程序员手册

21. 扩展RISC-V

22. ISA子集命名约定

23. 历史与致谢

### RISC-V手册下卷(riscv-privileged-v1.10)

1. 介绍

   1. 硬件平台术语

      hart：硬件线程

      核心(core)：包含独立取指单元的组件。

      协处理器(coprocessor)：是与RISC-V核心相连的一个单元，且主要被RISC-V指令流序列化，但包含了一些额外的体系结构状态和指令集扩展，相对于主RISC-V指令流来说可能会有些有限的自治。

      加速器(accelerator)：一个非可编程的固定功能单元，或可以自治工作、但专门用于某项任务的核心。

   2. 特权软件栈术语

      AEE：应用程序执行环境

      ABI：应用程序二进制接口

      SBI：管理员二进制接口

      SEE：管理员执行环境

      HBI：管理程序二进制接口

      HEE：管理程序执行环境

      HAL：硬件抽象层

   3. 特权级

      | 级别   | 编码   | 名字    | 缩写   | 特点          |
      | ---- | ---- | ----- | ---- | ----------- |
      | 0    | 00   | 用户/应用 | U    | 应用程序        |
      | 1    | 01   | 管理员   | S    | 操作系统        |
      | 2    | 10   | 管理程序  | H    | 虚拟机监视器      |
      | 3    | 11   | 机器    | M    | 必需的，可信的，最高级 |

   4. 调试模式：用以支持片外调试或制造测试，可以认为是一种比机器级更高的特权级。

2. 控制和状态寄存器(CSR)

   1. 访问CSR的指令：即RS32I里的CSR指令
   2. CSR地址映射约定：请阅读各个特权级的地址空间分配表
   3. CSR列表：请阅读各个特权级已分配的CSR地址

3. 机器级ISA

   1. 机器级CSR：

      - mcpuid, mimpid, mhartid
      - mstatus：硬件线程的当前操作状态
      - mtvex：机器自陷向量基址寄存器
      - mtdeleg：机器自陷转移寄存器
      - mip：机器中断挂起
      - mie：机器中断使能
      - mtime, mtimecmp
      - mscratch：记录机器模式上下文的地址
      - mepc：机器异常程序计数器
      - mcause：机器原因计数器
      - mbadaddr：机器坏地址寄存器

   2. 机器模式特权指令

      | funct12 | rs1  | funct3 | rd   | opcode | 意义               |
      | ------- | ---- | ------ | ---- | ------ | ---------------- |
      | ECALL   | 0    | PRIV   | 0    | SYSTEM | 向更高特权级发起请求       |
      | EBREAK  | 0    | PRIV   | 0    | SYSTEM | 将控制转回调试环境        |
      | ERET    | 0    | PRIV   | 0    | SYSTEM | 返回到自陷产生的特权级      |
      | MRTS    | 0    | PRIV   | 0    | SYSTEM | 将自陷处理从机器级转到管理员级  |
      | MRTH    | 0    | PRIV   | 0    | SYSTEM | 将自陷处理从机器级转到管理程序级 |
      | WFI     | 0    | PRIV   | 0    | SYSTEM | 等待中断             |

   3. 物理存储器属性：描述的是为物理地址区间指定属性的方法

   4. 物理存储器访问控制

   5. Mbare寻址环境

   6. 基址-边界环境

4. 管理员级ISA

   1. 管理员CSR：

      - sstatus: 管理员状态寄存器，追踪处理器当前的操作状态。
      - sip, sie：管理员中断寄存器，sip挂起中断，sie使能中断。
      - stime, stimecmp
      - sscratch：记录管理员模式上下文的指针
      - sepc：记录引起异常的指令的地址
      - scause：各种中断的来源
      - sbadaddr：记录发生取指异常的错误地址
      - sptbr, sasid

   2. 管理员指令

      | funct12   | rs1   | funct3 | rd   | opcode | 意义          |
      | --------- | ----- | ------ | ---- | ------ | ----------- |
      | SFENCE.VM | vaddr | PRIV   | 0    | SYSTEM | 更新存储器管理数据结构 |

   3. 管理员在Mbare环境中的操作

   4. 管理员在基址边界环境中的操作

   5. Sv32:基于页面的32位虚拟存储器系统

   6. Sv39:基于页面的39位虚拟存储器系统

   7. Sv48:基于页面的48位虚拟存储器系统

5. Hypervisor扩展

6. RISC-V特权指令列表

7. 平台级中断控制器(PLIC)

8. 机器配置描述

9. 历史