#### x模拟器的实现
1. x指令集目前(20171107)看起来很象Y86指令集，x模拟器也是从sim模拟器修改而来的。

2. 相较于Y86指令集，x指令集减掉了cmovXX指令，增加了or,not,shl,shr这4条指令，将mrmovl和rmmovl更名为load和store。增加了flags寄存器以取代Y86的条件码，并在flags寄存器上增加了分页位、中断位、CPU状态切换位。增加了pdir寄存器。

3. 对寄存器的修改

   * isa.h文件：修改枚举型**reg_id_t**(寄存器组)，删掉REG_R8~REG_R14，增加REG_PC，REG_FLAGS寄存器。
   * isa.c文件：修改数组**reg_table**(寄存器名与寄存器ID对应关系)，使与**reg_id_t**相适应。

4. 对指令集的修改

   * isa.h文件：修改枚举型**itype_t**(指令类型)，将I_RMMOVQ改为I_STOREQ，将I_MRMOVQ改为I_LOADQ。

     修改枚举型**alu_t**(算术运算指令类型)，增加A_OR, A_NOT, A_SHL, A_SHR。

   * isa.c文件：记录指令信息的数组**instruction_set**，每条都记录了[指令名，操作数，指令长度，参数1的类型，参数1的位置，参数1的属性，参数2的类型，参数2的位置，参数2的属性]。删除cmovXX指令，增加or,shl,shr这3条指令。

     修改函数compute_alt，增加了A_OR, A_SHL, A_SHR对应的功能。
