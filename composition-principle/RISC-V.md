## About the RISC-V Foundation

RISC-V (pronounced “risk-five”) is an open, free ISA enabling a new era of processor innovation through open standard collaboration. Founded in 2015, the RISC-V Foundation comprises more than 100 [members](https://riscv.org/members-at-a-glance/) building the first open, collaborative community of software and hardware innovators powering innovation at the edge forward. Born in academia and research, RISC-V ISA delivers a new level of free, extensible software and hardware freedom on architecture, paving the way for the next 50 years of computing design and innovation.

RISC-V是一个开放、自由的ISA，通过开放标准的协作使ISA进入了处理器创新的新时代。成立于2015年的RISC-V基金会由100多名成员组成，这是第一个开放的、协作的软件和硬件创新者社区，驱动着创新的前沿。RISC-V ISA诞生于学术界和研究领域，它提供了一种新的自由，在架构层面扩展软件和硬件的自由，为未来50年的计算设计和创新铺平了道路。

The RISC-V Foundation, a non-profit corporation controlled by its members, directs the future development and drives the adoption of the RISC-V ISA. Members of the RISC-V Foundation have access to and participate in the development of the RISC-V ISA specifications and related HW / SW ecosystem. The Foundation has a [Board of Directors](https://riscv.org/leadership/) comprising seven representatives from Bluespec, Inc.; Google; Microsemi; NVIDIA; NXP; University of California, Berkeley; and Western Digital.

RISC-V基金会,一个由其成员控制的非盈利的公司,指导着RISC-V ISA未来的发展并驱动着其适配。RISC-V基金会的成员可以访问并参与RISC-V ISA规范的发展和相关软/硬件的生态系统。基金会的董事会包含7个代表：Bluespec公司、谷歌、Microsemi、英伟达、NXP、加州大学伯克利分校和西部数据。

Each spring and fall, the expansive RISC-V ecosystem comes together to discuss current and prospective RISC-V projects and implementations, as well as collectively drive the future evolution of the instruction set architecture (ISA) forward. Workshop sessions feature leading technology companies and research institutions discussing the RISC-V architecture, commercial and open-source implementations, software and silicon, vectors and security, applications and accelerators, simulation infrastructure and much more. Learn more by visiting the [Workshop News](https://riscv.org/workshops/) and [Workshop Proceedings](https://riscv.org/category/workshops/proceedings/) pages.

每年的春天和秋季，广泛的RISC-V生态系统聚集在一起讨论当前和未来的RISC项目和实现，并共同推动指令集架构(ISA)的未来发展。领先的技术公司和研究机构引领研讨会的主题，讨论RISC-V架构、商业和开源的实现、软件和硅、矢量和安全、应用和加速器、仿真基础设施等等。通过访问[研讨会的新闻](https://riscv.org/workshops/)和[专题讨论](https://riscv.org/category/workshops/proceedings/) 会了解更多。

The RISC-V ISA was originally developed in the [Computer Science Division](http://www.cs.berkeley.edu/) of the EECS Department at the [University of California, Berkeley](http://www.berkeley.edu/).

RISC-V ISA最初开发于加利福尼亚大学伯克利分校的EECS系计算机科学部。

We encourage organizations, individuals and enthusiasts to join our ecosystem and together enable a new era of processor innovation through open standard collaboration.

我们鼓励组织、个人和热心人士加入我们的生态系统，通过开放标准协作，共同开创一个新的处理器创新时代。

## RISC-V ISA手册

本手册分为两卷，上卷讲用户级指令，下卷讲特权级指令。

### RISC-V 手册上册

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

   RV32I是基本指令集，它被设计足以支持一个现代的操作系统，而且除了A扩展外它基本上能模拟所有的其它扩展。

   1. 程序员模型

      用户可见的寄存器有3种：1个恒为0的x0，31个通用寄存器x1-x31，pc。

   2. 基本指令格式

   3. 立即数编码

   4. 整数计算指令

   5. 控制转换指令

   6. 内存操作指令

   7. 内存模型

   8. 控制和状态寄存器指令

   9. 环境调用

3. RV32E：RV32I的精简版，为嵌入式系统而设计

4. RV64I

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