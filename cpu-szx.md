### 概述
v9 computer是一个简化的计算机系统，包含一个简化的cpu v9，一块内存，时钟/键盘/屏幕三种外设。用于简化操作系统教学。代码详见root/bin/em.c。

### CPU抽象
可以把CPU看成一个黑盒。它从内存读取指令，向内存写入数据，和其它设备进行IO。要完成上述任务它需要有两个功能，一是控制指令流，二是运算。具体是由控制器和运算器实现以上两个功能的。控制器负责指令的获取、分析、执行。运算器存在的原因在于世间万物都可以抽象成数学运算。一个控制指令流，一个进行数学运算，CPU的功能就完备了。再进一步划分控制器可以分为程序计数器、指令寄存器、指令译码器、时序产生器和操作控制器，而运算器可分为算术逻辑单元(ALU)、累加寄存器、数据缓冲寄存器和状态条件寄存器。

### 数据结构
- a: 通用寄存器a
- b: 通用寄存器b
- c: 通用寄存器c
- f: 浮点寄存器f
- g: 浮点寄存器g
- ir:　指令寄存器，用来保存当前正在执行的一条指令
- xpc: pc在host内存中的值
- fpc: pc在host内存中所在页的下一页的起始地址值
- tpc: pc在host内存中所在页的起始地址值
- xsp: sp在host内存中的值
- tsp: sp在host内存中所在页的起始地址值
- fsp: 辅助判断是否要经过tr/tw的分析
- ssp: 内核态的栈指针
- usp: 用户态的栈指针
- cycle: 指令执行周期计数
- xcycle:　用于判断外设中断的执行频度，和调整最终的指令执行周期计数（需进一步明确?）
- timer: 当前时钟计时（和时间时间中断有关）
- timeout: 时钟周期，当timer>timeout时，产生时钟中断
- detla:　一次指令执行的时间，timer+=delta

### v9 CPU(详见函数cpu)
- 输入程序计数器pc(实际上是目标文件的入口地址)和栈寄存器sp（实际是手动申请内存与默认分配内存之差），无返回值。程序计数器存放的是下一条指令在内存中的地址。
- 各段的含义推测
  - fixsp: 为xsp, tsp，fsp赋值，然后接一个for循环，基本上包含了cpu函数的绝大部分内容
  - fixpc: 为xcircle,xpc,tpc,fpc赋值，处于for循环的起始位置，基本上包含了cpu函数的绝大部分内容
  - next: 处于for循环之后，基本上包含cpu函数的绝大部分内容
- 重要函数作用推测
  - rlook: 通过调用setpage建立TLB的映射关系(pde,pte只在rlook,wlook函数中出现)
  - wlook: 通过调用setpage建立TLB的映射关系(pde,pte只在rlook,wlook函数中出现)
  - flush: 清空TLB(页表缓冲)内的数据
  - setpage:向TLB写入数据
  - usage: 介绍虚拟机的用法
- 寄存器
- 指令集(共有209个指令)
```
  1.系统和扩展类指令
  HALT  ENT LEV JMP JMPI  JSR JSRA  LEA LEAG  CYC MCPY  MCMP MCHR MSET
  2.访存指令
  L代表将内存中的数据载入到寄存器a中，LB代表将内存中的数据载入到寄存器b中
```


- sp,xsp,tsp,fsp之间的关系
  - sp类的指针都和栈的位置有密切关系
  - xsp指向当前栈的栈底，栈的方向是向下减少的
  - fsp与xsp在页内的剩余空间有关，这个剩余空间右移8位即是fsp
  - v = xsp -tsp      //v是xps对当前页的偏移地址
  - p = tw[v >> 12]   //p是v对应的页地址
  - xsp = v ^ (p - 1) //就是当前栈指针
  - tsp = xsp - v     //就是当前栈所在页的地址
  - fsp = (4096 - (xsp & 4095)) << 8
- pc,xpc,tpc,fpc之间的关系
  - v = xpc - tpc     //v是xpc在其所在页的偏移量
  - p = tr[v>>12]     //p是v对应的页地址
  - p = rlook[v]
  - xcycle -= tpc     //单独看没有意义，与xpc和cycle结合使用才有意义，可以勉强认为它是个计数器
  - xpc = v ^ (p -1)  //xpc就是程序计数器，指向下一条指令
  - tpc = xpc - v     //tpc是xpc所在页的地址
  - xcycle += tpc
  - fpc = (xpc + 4096) & -4096  //fpc指向xpc所在页的下一页的起始地址
  - 真正的循环次数：cycle - (xcycle - xpc) / 4 ,而且看起来xpc始终小于xcycle
- trk,twk,tru,twu
  - t代表页表
  - r代表读
  - w代表写
  - k代表内核
  - u代表用户
  - v代表页在页表内的编号
