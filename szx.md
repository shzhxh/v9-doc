2018.01.16~2018.01.19

对zcu102平台使用方法的记录：[zcu102.md](https://github.com/shzhxh/v9-doc/blob/master/LabeledKernel/zcu102平台使用.md)

下半年学习科研总结：[下半年总结.md](https://github.com/shzhxh/v9-doc/blob/master/docs/2017下学年总结.md)

2017.12.30~2018.01.15

- 成功复现了张蔚的工作，记录了安装过程：[bbl-ucore安装过程](https://github.com/shzhxh/v9-doc/blob/master/OS/installation-szx.md)
- 阅读完毕bbl-ucore的文档，记录了阅读过程：[lab1](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab1-szx.md),[lab2](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab2-szx.md),[lab3](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab3-szx.md),[lab4](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab4-szx.md),[lab5](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab5-szx.md),[lab8](https://github.com/shzhxh/bbl-ucore/blob/szx-testing/docs/lab8-szx.md)
- 记录了对RISCV指令集的学习过程：[RISC-V.md](https://github.com/shzhxh/v9-doc/blob/master/composition-principle/RISC-V.md)

2017.12.23~2017.12.29

- 读《flex与bison》，做了一些笔记：[flex&bison](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/flex%20and%20bison.md)
- 看到有人写编译器用flex与bison生成前端，用llvm做后端，在网上搜了一下llvm的作用。似乎llvm能做的事还挺多，比如生成一个模拟器。我想多了解一点llvm。

2017.12.16～2017.12.22

- 本周主要听Mr. 黄讲解android-x86的相关知识，做了一点初步的记录：[android-x86.md](https://github.com/shzhxh/v9-doc/blob/master/android-x86-porting.md)

2017.12.9~2017.12.15

1. 使用lex和bison搭建了一个编译器的框架，正在尝试实现一些简单的语言特性。
2. 感觉语言设计和操作系统之间是协同发展的关系，就像C和Unix一样。
3. 由于语言设计和操作系统的需要，模拟器已实现中断，增加了对标签的支持，增加了int和iret指令。对模拟器新添加的功能进行了简单的测试。
- 下周计划：
  * 向下继续实现开发板上的CPU
  * 向上继续实现编译器和操作系统

2017.12.2~2017.12.8

- 计算机组成原理课
  * 已完成秒表实验：[clock.md](https://github.com/shzhxh/v9-doc/blob/master/composition-principle/clock.md)
  * 未进行ALU实验
- 编译原理课
  - 未进行实验。
- 新v9
  - step3已完成。模拟器实现了较完善的指令集，但在**中断管理**的实现上还有点问题。ISA目前有10个寄存器和27条指令。[ISA.md](https://github.com/shzhxh/v9-doc/blob/master/ISA.md)主要描述了ISA的设计思路，[step3.md](https://github.com/shzhxh/v9-doc/blob/master/docs/step3.md)主要描述了ISA的实现方法。
- 下周计划
  * 模拟器和汇编器已基本完成，不会再主动更新了。接下来重点是向上逐步实现xv6，向下逐步实现真实的CPU，并在此过程中验证ISA是否正确。实现新v9编译器的过程和编译原理课是重合的，所以我计划只实现新v9的编译器而不再做编译原理课的实验了，但会参考decaf的设计思想。
  * 计算机组成原理：继续完成ALU的实验。
  * 新v9的编译器：思考新语言的特性，完成编译器的框架。
  * 新v9的操作系统：ucore系统是通过逐步添加功能的方式而实现的，我考虑是否可以换种方式，先搭好xv6的框架，再在框架的基础上逐步完善成一个操作系统。
  * step4的目标概括来说，就是实现编译器和OS的框架。

2017.11.26~2017.12.01

- 计算机组成原理课
  - 本周计划完成一个秒表实验。用VHDL和Verilog都尝试了一下，均未能成功。我想秒表实验的电路图是挺简单的，是对硬件描述语言不够熟悉。同时考虑到开发板附带资料是用Verilog写的，所以我决定选用Verilog进行接下来的实验。我选择的参考书是《Verilog数字系统设计教程》(第2版，夏宇闻)。做了一些学习笔记：[Verilog.md](https://github.com/shzhxh/v9-doc/blob/master/composition-principle/VerilogHDL.md)。
- 编译原理课
  - 本周计划完成PA2之前的所有实验。未完成，进展缓慢。学习笔记在[decaf-pa1a.md](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/decaf-pa1a.md)的最后部分增加了两个问题：pa1a的处理进程，同名变量在不同文件中的含义。
- 新的v9项目
  - 本周计划完成step2:扩充指令集，已完成。关于step2的说明：[step2.md](https://github.com/shzhxh/v9-doc/blob/master/docs/step2.md)。
- 下周计划
  1. 计算机组成原理：继续完成秒表实验，并完成ALU的实验。
  2. 编译原理：deadline就在本周了，我还是想争取完成PA2之前的所有实验。
  3. 新v9：对现有指令集进行测试，考虑中断指令和内存管理指令的实现问题，对现有指令集进行少量扩展。

2017.11.18~2017.11.25

* 计算机组成原理课
  - 本周主要是看了点HDMI的原理，然而并没有看懂。感觉自己的方向有点偏了，还是应以实验指导书为主的。
* 编译原理课
  - 无。
* 新的v9项目
  - 突然感觉为什么要把所有的东西一下子都设计好呢？一个看起来stupid但可运行的东西比一个perfect但不能运行的东西要更好。于是实现了[step1](https://github.com/shzhxh/v9-doc/blob/master/docs/step1.md)，一个支持打印Hello World的虚拟机+编译器+操作系统。
* 下周计划
  1. 计算机组成原理：完成一个用LED灯完成秒表的实验。
  2. 编译原理：完成包括PA2之前的所有实验。
  3. 新v9：实现step2，扩充指令集，实现基本指令(不包含中断，特权级和内存管理)，时间允许的话还想再实现一个debuger(单步跟踪，打印处理器和内存状态)。

2017.11.11~2017.11.17

* 计算机组成原理课
  1. 实验环境介绍：[platform.md](https://github.com/shzhxh/v9-doc/blob/master/composition-principle/platform.md)
  2. 一个控制LED灯的实验：[led.md](https://github.com/shzhxh/v9-doc/blob/master/composition-principle/led.md)
* 编译原理课
  1. 对讲义的理解：[概要介绍](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/lecture1.md)，[词法分析](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/lecture3-lexical.md)，[语法分析](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/lecture4_5.md)
* 新的v9项目
  1. 对中断、特权级的考虑，详见[ISA.md](https://github.com/shzhxh/v9-doc/blob/master/ISA.md)黑体标注的部分
* 下周计划

2017.11.07～2017.11.10

* v9：新设计的指令集目前看起来和CSAPP的Y86指令集是很像的，不同之处在于：

  1. 去掉了带条件的mov指令，我目前还不清楚带条件的mov指令有什么作用。
  2. 增加了3条指令，分别是or, shl, shr。感觉这些指令比较常用，而且没法用其它指令替代。
  3. 增加了pdir寄存器用来在分页的时候保存页目录。
  4. 增加了状态寄存器，用以实现分页、中断、用户态和内核态切换，算术运算溢出控制。

  关于指令集及编码，见[ISA.md](https://github.com/shzhxh/v9-doc/blob/master/ISA.md)的最后一部分：指令与机器码对照表。

* v9：新模拟器是在CSAPP的ysi的基础上改出来的，现在只能编译通过，但是否正确还需要检验。具体修改过程详见：[simulater-x.md](https://github.com/shzhxh/v9-doc/blob/master/simulator/simulator-x.md)：x模拟器的实现。

* 编译原理课：我发现要完成实验，先要读懂讲义，要读懂讲义先要理解形式语言与自动机的理论。

  1. 形式语言与自动机的阅读材料，我按自己的理解又描述了一遍，详见[formal-language.md](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/formal-language.md)。
  2. 在学习讲义的时候我感觉讲义的形式还是有可以改进的地方。人的认知规律是先明白意义，才能遵守规则。由感性认识而渐近于理性认识的。而讲义里只有规则，而没有说明规则的意义。只有定义和公式的堆砌，没有说明那些定义和公式的来源。不像是为学习者准备的资料，而像是专家备查的工具书。
  3. 为了实现pa1a的实验，我尝试通过跟踪decaf源文件里字符串的转化过程来理解编译器究竟做了些什么。详见[decaf-szx.md](https://github.com/shzhxh/v9-doc/blob/master/compiling-principle/decaf-pa1a.md)的最后部分：跟踪注释的处理过程和跟踪关键字的处理过程。

* 目前我的问题一是进度缓慢，需要对deadline重视起来。二是学习过程的文档记录不够细致。三是效率不高容易被外界干扰。需要在以后的工作中加以克服。

2017.11.07

* 考虑到v9项目、CSAPP和在清华学的3门课是有内在联系的，我想把它们结合起来，变成一个新的v9。[我的整体考虑](https://github.com/shzhxh/v9-doc/blob/master/README.md) 
* 必须先有一个ISA，目前只大致想好了指令，在考虑指令是否完备及机器码的设计。[目前的ISA](https://github.com/shzhxh/v9-doc/blob/master/ISA.md) 
* [对CSAPP的模拟器的分析](https://github.com/shzhxh/v9-doc/blob/master/simulator/simulator.md) 。

2017.10.21~2017.11.03

* 做编译原理和计算机原理的作业
  * 编译原理主要是分析了pa1a阶段源文件，理解了从decaf源文件到抽象语法树的生成过程，程序尚未调试通。[我的一点分析](https://github.com/shzhxh/v9-doc/decaf) 
  * 计算机原理只是听课，还没有怎么看。
* 继续学习xv6
  * 主要比较了v9和x86上关于内存管理和进程管理方面的异同。[我的一点分析](https://github.com/shzhxh/v9-doc/blob/master/xv6-szx.md) 
* 学习JavaScript
  * 目前是在[廖雪峰的网站](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/) 学习JavaScript
* 4个源文件分析工具
  * 我下载到本机尝试编译，均未能编译通过。

2017.10.14~2017.10.20

- 本周工作计划点1：完成计算机原理课的实验二
  - 完成情况：未完成，在新电脑上安装Linux系统就花了四天时间，最后发现失败原因出在驱动上。
- 本周工作计划点2：通过微信小程序，完成编译原理课的习题和实验。
  - 完成情况：未完成，正在补背景知识，形式语言与自动机。
- 本周工作计划点3：完成xv6 step by step的构建过程。
  - 完成情况：未完成。有点太乐观估计自己的进度了。目前依然在尝试理解并分解进程部分。

- 下周计划：
  1. 做编译原理和计算机原理的作业。
  2. 继续划分xv6。
  3. 学习JavaScript。

2017.09.23~2017.10.13

- 本周工作计划点1：按时听课，做好笔记。
  - 完成情况：计算机原理课完成了实验一，熟悉了thco mips指令系统。编译原理课尚未完成实验。
- 本周工作计划点2：划分xv6的代码。
  - 完成情况：目前实现到第三步，完成了内存分页。
- 本周工作计划点3：读懂v9机器的CPU代码。
  - 完成情况：已完成。

- 下周计划：
  1. 完成计算机原理课的实验二。
  2. 通过微信小程序，完成编译原理课的习题和实验。
  3. 完成xv6 step by step的构建过程。

2017.09.16~2017.09.22

- 本周工作计划点1：按时听课，做好笔记。
  - 完成情况：了解了计算机的冯诺依曼结构和哈佛结构，了解了PC在CPU设计中的重要作用，了解到编译的具体过程
- 本周工作计划点2：学习github里如何创建分支及提交
  - 完成情况：已完成。实际上是git命令的使用，分支的本质实际上是一颗树
- 本周工作计划点3：学习v9的硬件结构，并优化相关的markdown文档
  - 完成情况：未完成。v9计算机的实现代码难以理解，但在硬啃的过程中也学到一些C语言的技巧，这种编程风格像给C语言加了密似的。另外，底层机器指令比较臃肿，方便实现，但不方便理解和使用。我考虑是否可以将指令集替换成计算机组成原理课上的MIPS指令集，只有44条指令，看上去优雅的多了。
- 本周工作计划点4：学习v9上ucore操作系统的8个实验，并优化相关markdown文档。
  - 完成情况：未完成。
- 本周工作计划点5：将v9上的xv6操作系统拆分成若干阶段，并写出相关markdown文档。
  - 完成情况：未完成。对这件事难度估计不足，而且 v9上的xv6有点像ucore,不太像xv6了。我从github获取了xv6的最初代码，我想在最初代码的基础上划分xv6。
- 本周工作计划点6：继续学习JavaScript。
  - 完成情况：未学习，浏览器上的操作系统有很好的跨平台性，但速度慢，debug信息不足，难以调试，感觉还是c语言实现版较好。这一点需要和陈老师进一步探讨。
- 本周工作计划点7：了解一下基于xv6的图形界面。
  - 完成情况：没有看，觉得图形界面很重要而且也有一些想法，但现在还是想把重心放在底层原理上。


- 下周计划：
  1. 按时听课，做好笔记。
  2. 分析xv6的初始代码，划分成若干个模块，然后再考虑为实现这些模块怎么划分step。ucore共划分出38个step，所以xv6也应该可以划分出40个左右的step。
  3. 把v9的指令集改造成计算机组成原理课上的MIPS指令集，用编译原理课上的知识来优化v9的编译器，甚至也可以仿照C语言的设计思想设计一个更简单且足够强大的语言进行v9上操作系统的开发，这只是个人设想，我需要和陈老师进一步探讨这样做的必要性和可行性。

2017.09.09~2017.09.15
- 本周工作计划点1：学习SICP
  - 完成情况：了解了到编程就是要控制复杂性，控制复杂性的三个方法和学习编程语言的三个要点。陈老师说SICP有点难，我决定先停止SICP的学习。
- 本周工作计划点2：对JavaScript的语法做一个概要性的总结
  - 完成情况：在实验楼有一个JavaScript的入门教程，我学习完毕，对JavaScript有了一个比较直观的认识。
- 本周工作计划点3：完成对v9编译部分的解析
  - 完成情况：部分完成，主要分析了标记(token)和C语言的对应关系。


- 论文阅读进展：
  - 无


- 课题实践进展：
  - 无


- 下周计划：
  1. 按时听课，做好笔记。
  2. 学习github里如何创建分支及提交。
  3. 学习v9的硬件结构，并优化相关markdown文档。
  4. 学习v9上ucore操作系统的8个实验，并优化相关markdown文档。
  5. 将v9上的xv6操作系统拆分成若干阶段，并写出相关markdown文档。
  6. 继续学习JavaScript。
  7. 了解一下基于xv6的图形界面。


- 其它事宜：
  - 无

2017.09.05~2017.09.08

- 本周工作计划点1:熟悉周报提交流程

- 完成情况：已完成。打开邮箱，按照邀请邮件的提示操作即可。

- 本周工作计划点2:理解v9.js的整体结构,并分析完毕编译部分

- 完成情况：部分完成。目前只能说是浏览了v9的目录结构，对v9的目录功能做了一些推测，想准确的理解v9源代码需要一些背景知识，包括计算机原理、编译原理、JavaScript语言，我需要在接下来的时间里尽快补足这些背景知识。对于编译部分，主要阅读了xvcc.c文件的main函数和next函数，做了一些总结，但并没有分析完毕，语言方面也需要进一步整理以方便他人理解。

- 论文阅读进展：
  - 无


- 课题实践进展：
  - [课题进展纪要](../developers/shizhenxing/index.md)


- 下周计划：
  1. 发现SICP(计算机程序的构造与解释)所讲述的内容与v9.js想完成的目标有较大的相关性，下周计划开始学习SICP以作为v9.js的某种参考。
  2. 对JavaScript的语法做一个概要性的总结
  3. 完成对v9编译部分的解析
- 其他事宜：
  无
