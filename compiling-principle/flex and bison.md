- install:

  `sudo apt install flex bison`

- 作用：

  flex: 生成一个确定有穷自动机(DFA)，使用正则表达式描述正则文法。

  bison:使用BNF范式描述上下文无关文法，生成的应该是下推自动机。

- 结构：flex和bison的程序均由三部分构成

  ```
  声明
  %%
  规则
  %%
  C代码
  ```

  > %{和%}之间的代码直接照抄到生成的C文件中

- flex的语法：

  - 正则表达式
  - 消除二义性：最大匹配，最早匹配
  - yyin: 读取输入的文件句柄（输入管理的第一个层次）
  - YY_BUFFER_STATE:创建并使用输入缓冲区（输入管理的第二个层次）
  - YY_INPUT:重定义输入到当前缓冲区的宏（输入管理的第三个层次）
  - yyout:所有没有被匹配的输入都会复制到yyout，可使用`%option nodefault`禁用它
  - flex的库文件： -lfl，定义了默认的main例程和yywrap，然而基本上所有的flex程序都使用`%option noyywwap`和自己的主例程，所以它们并不需要flex库。
  - yytext: 字符串
  - yylex(): 读取一个部分输入，返回下一个记号。记号由两部分组成：记号的编号和记号的值(yyval)。
  - yyrestart(f):使词法分析器读取标准输入输出文件f。
  - yyterminate():立即从词法分析器返回。
  - %x IFILE:把IFILE定义为起始状态。
  - INITIAL：flex本身定义的状态
  - %s：声明包含的起始状态
  - BEGIN：切换到另外一个起始状态
  - `<<EOF>>`：匹配输入文件的结束

- bison的语法：

  - %token: 记号名称。
  - 起始符号在第一条规则的左边。