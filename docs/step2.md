#### step2简介

进入step2的代码：`git reset --hard xxx	//xxx为step2对应的id`

编译step2：`make`

清理编译代码：`make clean`

运行编译器：`./assembler Hello.s`

运行模拟器：`./simulator Hello.o`

step2在step1的基础上增加了7个寄存器，实现了算术逻辑指令、rrmov指令、push和pop指令。

现在有8个32位的寄存器，四个通用寄存器ra,rb,rc,rd，栈指针寄存器sp。其它寄存器bp,si,di需要与相关指令配合使用，目前和通用寄存器并无差别。

指令集如下：

| 指令格式                  | 编码                | 占用字节数 | 含义                |
| --------------------- | ----------------- | ----- | ----------------- |
| halt                  | 0x00              | 1     | shut down         |
| nop                   | 0x10              | 1     | do nothing        |
| rrmov %reg1, %reg2    | 0x20+r1r2         | 2     | r2 = r1           |
| irmov imme, %reg2     | 0x30+0xF(r2)+imme | 6     | r2= imme          |
| store %reg1, D(%reg2) | 0x40+r1r2+D       | 6     | 未实现               |
| load D(%reg1), %reg2  | 0x50+r1r2+D       | 6     | 未实现               |
| add %reg1, %reg2      | 0x60+r1r2         | 2     | r2 += r1          |
| sub %reg1, %reg2      | 0x61+r1r2         | 2     | r2 -= r1          |
| and %reg1, %reg2      | 0x62+r1r2         | 2     | r2 &= r1          |
| or  %reg1, %reg2      | 0x63+r1r2         | 2     | r2 \|= r1         |
| xor %reg1, %reg2      | 0x64+r1r2         | 2     | r2 ^= r1          |
| shl %reg1, %reg2      | 0x65+r1r2         | 2     | r2 <<= r1         |
| shr %reg1, %reg2      | 0x66+r1r2         | 2     | r2 >>= r1         |
| jmp Dest              | 0x70+Dest         | 5     | pc = Dest         |
| je Dest               | 0x71+Dest         | 5     | 仅编译器实现            |
| jl Dest               | 0x71+Dest         | 5     | 仅编译器实现            |
| jg Dest               | 0x71+Dest         | 5     | 仅编译器实现            |
| jne Dest              | 0x71+Dest         | 5     | 仅编译器实现            |
| jle Dest              | 0x71+Dest         | 5     | 仅编译器实现            |
| jge Dest              | 0x71+Dest         | 5     | 仅编译器实现            |
| call Dest             | 0x80+Dest         | 5     | 仅编译器实现            |
| ret                   | 0x90              | 1     | 仅编译器实现            |
| push %reg1            | 0xa0+(r1)0xF      | 2     | [sp-4]=r1; sp-=4; |
| pop %reg1             | 0xb0+(r1)0xF      | 2     | r1=[sp]; sp+=4;   |
| in imme, %reg2        | 0xc0+0xF(r2)+imme | 6     | 仅编译器实现            |
| out %reg1, imme       | 0xd0+(r1)0xF+imme | 6     | (imme) = r1       |

注释：

1.  (r1)0xF：占用一个字节，r1的编码占高4位，0xF占低4位
2.  0xF(r2)：占用一个字节，r2的编码占低4位，0xF占高4位
3.  r1r2：占用一个字节，r1的编码占高4位，r2的编码占低4位
4.  D, Dest, imme：均指32位立即数
5.  r1, r2, sp, pc：均指32位寄存器
6.  [r1]：指针r1代表的内存单元
7.  (imme)：立即数imme代表的端口

#### 编译器

1. 编译器执行的大致过程？

   1. 打开源文件和目标文件。

   2. 由yylex()函数完成从源代码到目标代码的任务。

   3. 设计了一个结构体来保存源文件的每条汇编语句。

      ```c
      struct {
        int id;			// 指令在枚举型里的编号
        char instr;		// 指令的代码
        char reg[2];		// 一条指令里最多出现两个寄存器
        int reg_index;	// 寄存器的编号
        int imme;			// 一条指令里最多出现一个立即数
      } statement =
      {0, 0, {0xf, 0xf}, 0, 0};
      ```
   4. 依据每条指令的不同将结构体中相应的数据写入目标文件。

   5. 关闭源文件和目标文件。

2. 如何识别寄存器？

   - 寄存器放在一个枚举类型里。

   - 创建了一个寄存器的数组，数组的每一项都是一个结构体。

     ```c
     struct {
       const char *name;		// 寄存器名称
       const char id;		// 寄存器在枚举型中的编号
       unsigned int value;	// 寄存器的值
     } reg_table[];
     ```
   - 通过枚举型的编号和结构体里的id项来定位具体的寄存器。

3. 如何识别指令？

   - 指令编号放在一个枚举类型里。
   - 创建了一个指令的数组，数组的每一项都是一个结构体。
   - 通过枚举型的编号和结构体里的id项来定位具体的指令。

4. 分号的重要作用？

   - 分号是一条语句结束的标志
   - 遇到分号会执行将暂存在结构体里的数据写入目标文件
   - 因为分号的作用，把上下文无关语言转换成了正则语言

#### 模拟器

1. 模拟器执行的大致过程？

   1. 打开目标文件。
   2. 把目标文件载入内存。
   3. 执行内存里目标文件代表的指令。
   4. 关闭目标文件。

2. 大端还是小端？

   与x86CPU保持一致，是小端系统。我感觉大端和小端就是人类习惯的问题，对于CPU来说是没有差别的。

3. 指令的判别？

   直接通过指令的编号来判别。

4. 寄存器的判别？

   ```c
   switch (index) {
     case 1:
       return reg >> 4;
     case 2:
       return reg & 0xF;
     default:
       return 0xFF;
   }
   ```

   ​

5. 栈是向上增长还是向下增长？

   栈是向下增长的，与x86系统保持一致。

6. 出栈与入栈的次序？

   ```c
   // 入栈
   mem[sp - 4] = reg;
   sp -= 4;
   // 出栈
   reg = mem[sp];
   sp += 4;
   ```

   ​

