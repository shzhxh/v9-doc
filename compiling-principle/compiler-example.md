### 什么是编译器？
  个人认为，编译器是从一个指令集到另一个指令集的**映射工具**。为实现此功能，任何编译器都需要做这3件事，1是**分解**原指令集，2是**分析**原指令集，即将原指令集转化为等价于目标指令集的结构，显然这个结构也是等价于目标指令集的，3是将所得的结构**重组**成目标指令集。以v9.js为例，它也做的是这三件事：1构建词法分析器，2构建语法分析器，3构建虚拟机及指令集。

### 四个主要的函数
    * next:获取下一个标记，用于词法分析  
      所有支持的标记都被放入一个枚举类型中，词法分析器的框架，换行符，宏定义，标识符与符号表，数字，字符串，注释，lookahead，关键字与内置函数
    * decl:语法分析，使用递归下降的方法  
      BNF语法，左递归的消除  
      变量定义：EBNF表示，解析变量的定义  
      函数定义：函数定义的解析，解析参数，函数体的解析
    * expr:解析一个表达式  
      stmt函数：解析一个语句（IF，WHILE，RETURN，BREAK，CONTINUE，FOR，DO，SWITCH，CASE,ASM,VA_START,DEFAULT,GOTO,ID,其它语句）
      运算符的优先级：逆波兰式，一元运算符，二元运算符

### 一个简化的计算机
    * 内存:text(代码段)，data(数据段)，stack(栈)
    * 寄存器：pc(程序计数器)，sp(指针寄存器)，bp(基址寄存器)，ax(通用寄存器)
    * 指令集：一个字符型数组ops[]和一个枚举类型
      1. IMM(将立即数放入ax)，LC(将字符地址放入ax)，LI(将整数地址放入ax),SC(将ax中的数据放入栈中),SI(将ax中的数据放入栈中)
      2. PSHA(将ax的值入栈)
      3. JMP(无条件跳转)
      4. JZ/JNZ(ax为零/不为零跳转)
      5. CALL调用，RET返回，ENT子函数调用，ADJ清空栈中子函数的参数，LEV返回，LEA参数调用
      6. 运算符指令，要注意第一个参数在栈顶，第二个参数在ax中
      7. 内置函数：exit,open,close,read,printf,malloc,memset,memcmp

### 全局变量
    * file字符型指针，用于指向预处理包含文件的文件名或源文件的文件名
    * cmd字符型指针，用于指向命令的名字
    * pos字符型指针，用于指示源文件中字符的位置
    * incl字符型指针，用于指示预处理包含文件的路径

    * tk整型变量，用于记录当前标记
    * ts、ip整型变量，分别指代码段和代码指针
    * gs、data整型变量，指数据段、当前偏移
    * bss整型变量，bss偏移
    * loc整型变量，本地帧偏移
    * line整型变量，用于记录行号
    * ival整型变量，当前标记的整数值
    * errors整型变量，错误的数量
    * verbose整型变量，c编译器的-v选项，输出详细信息
    * debug整型变量，c编译器的-s选项，打印源和生成代码
    * ffun整型变量，末解决的函数计数器
    * va、vp整形变量，分别指变量池和变量指针
    * e整型变量的指针，是表达式树的指针
    * pdata整型变量的指针，数据段修补指针
    * pbss整型变量的指针，BSS段修补指针

    * fval双精度型变量，当前标记的双精度型变量

    * ty无符号整型变量，当前解析的子表达式类型
    * rt无符号整型变量，当前解析的函数返回类型
    * bigend无符号整型变量，大端机器
    * info_fd文件句柄
    * id：ident_t结构体的指针，当前解析标识符

### 标记与汇编码
  - 标记
![标记](tokens.png)
  - 汇编码
![汇编码](assembly.png)

### 静态变量
    * iline静态整型变量，预处理包含文件所在的行
    * ifile静态字符型指针变量，可能代表预处理包含文件的句柄
    * ipos静态字符型指针变量，意指预处理包含文件在文件中的位置
    * iname静态字符型数组，保存着预处理包含文件的路径的字符串
    * brk静态整型变量，

### main函数
    * 输入整型变量argc是变量个数，argv是字符型指针变量的数组，输出整型变量
    * 数据结构：  
      hdr结构体：看起来像是在模拟硬盘
      outfile字符型指针：输出文件的文件名
      tmain:ident_t结构体的指针，
    * 工作流程：获取编译参数，为目标代码申请内存空间，将源文件读入内存，将源代码转换为目标代码，将目标代码写入文件中。

### next函数
    * 详见src/xvcc.c文件
    * 无输入，无输出，操作的均为静态变量和公共变量。把源码输出为标记流。
    * 所有标记都放在一个枚举型变量中,注意标记是有顺序的
    * 私有变量：p,hm
      * b整型变量，表明一个字符串或文件的长度
    * 处理逻辑：  
      * 如果是空格，水平制表'\t'，垂直制表'\v'，回车'\r'，换页'\f'，则跳出本循环，读下一个字符。  
      * 如果是换行'\n'，则行数加1,跳出本循环，读下一个字符。
      * 如果是'#'，则判断是否是预处理语句"#include"，依次读取预处理包含文件。
      * 如果是字母、下划线或美元符号"$"，
      * 如果是数字，
      * 如果是斜杠“/”，可能是除号或注释。
      * 如果是斜杠"\"或"，判断是哪种转义字符。
      * 如果是“=”，可能是Eq或Assign。
      * 如果是运算符,则为tk赋值相应的标记。
      * 如果是其它符号,则为tk赋值相应的标记。
```
     变量id(Id), Ptr, Notf, Nzf,  Fun, FFun, 函数调用Fcall, 符号标记(Label, FLabel),
   Cic, Cuc, Cis, Cus,  Addaf, Subaf, Mulaf, Dvua,
  Divaf, Mdua, Srua,
* 关键字标记(括号内为标记)
asm(Asm) auto(Auto) break(Break) case(Case) char(Char) const
continue(Continue) default(Default) do(Do) double(Double)
else(Else) enum(Enum) extern float(Float) for(For) goto(Goto)
if(If) int(Int) long(Long) register return(Return) short(Short)
signed sizeof(Sizeof) static(Static) struct(Struct)
switch(Switch) typedef(TypeDef) union(Union) unsigned(Unsigned)
void(Void) volatile while(While)
va_list(Va_list), va_start(Va_start), va_arg(Va_arg),
整数Num, 浮点数Numf, 多个点Dots,
汇编指令Lea, 汇编指令Leag,汇编指令Cid, 汇编指令Cud, 汇编指令Cdi, 汇编指令Cdu,
汇编指令Eqf, 汇编指令Nef, 汇编指令Ltu, 汇编指令Ltf, 汇编指令Geu, 汇编指令Gef, 汇编指令Sru, 汇编指令Addf, 汇编指令Subf, 汇编指令Mulf,汇编指令Dvu, 汇编指令Divf, 汇编指令Mdu,
```
```
* 运算符标记，从上到下优先级降低
()Paren []Brak ->Arrow .Dot
!Not ~ ++Inc --Dec + - * (type) sizeof
*Mul /Div %Mod
+Add -Sub
<<Shl >>Shr
<Lt <=Le >Gt >=Ge
==Eq !=Ne
&And
^Xor
|Or
&&Lan
||Lor
?:Cond
=Assign +=Adda -=Suba *=Mula /=Diva %=Moda &=Anda ^=Xora |=Ora <<=Shla >>=Shra
,Comma
```

### stmt函数
  * 无输入，无输出，操作的均为静态变量和公共变量。把语句解析成汇编代码。
  * 私有变量：c, d, et, cmin, cmax  
    a看起来像是语句的某个跳转入口  
    b看起来像是语句的某个跳转入口  
    es用于存储表达树的指针  
  * 处理逻辑：
    * IF语句：判断表达式的值，为真则顺序执行然后跳转到语句末尾，为假则跳转到else语句。
    * WHILE语句：判断表达式的值，为真则顺序执行然后跳转到语句前端，为假则跳到语句末尾。
    * RETURE语句：使用LEV指令返回函数。
    * Break语句：

### expr函数
  * 输入整型，无返回值。把表达式解析成汇编代码。
  * 如果是数字，标记为Num或Numf。
  * 如果是字符串，标记为Leag。
  * 如果是Id,
  * 如果是Va_arg,
  * 如果是Sizeof，

### decl函数
  - 输入整型，无返回值。

### info_函数
  * info_open输入文件名的指针，无返回值。打开文件，将文件句柄赋值给公共变量，为ip赋值。
  * mapfile输入文件名的指针，输出字符型指针，将文件内容载入到内存中。

### 指令发射器(introduction emitter)
  * em,emi,emj,eml,emg,emf,patch

### 分析器
  * next, imm, immf, tsize, tinc, talign, basetype, type, decl, member
  * dline是个分析器，将行中的某个位置到行末的距离输出到句柄为info_fd的文件中。
  * skip输入整型变量c,无输出，作用是跳过标记c。  

### 表达式分析
  * node, nodc, mul, add, flot, ind, addr, assign, trim, cast, expr,

### 表达式生成
  * lmod, smod, lbf, opf, opaf, lbi, lb, opt, opi, op, opa, rv, stmt
  * testnot输入表达式树指针和一个整数，返回一个整数。
  * test输入表达式树的指针和一个整数，返回一个整数。
  * opt输入表达式树指针，无返回值。
