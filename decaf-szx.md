### 实验pa1a

#### 源代码的结构

1. Lexer.l用以生成Lexer.java文件，作用是把decaf文件的源代码转化为Token的形式，供Parser.java使用，完成的是词法分析的功能。是用正则表达式来实现此功能的。
2. Parser.y用以生成Parser.java文件，作用是把从Lexer.java得到的Token转化为语法树，完成的是语法分析的功能。是用文法规则实现此功能的。
3. 抽象类BaseLexer是作为类Lexer的父类而出现的。
4. 抽象类BasePaser是作为类Paser的父类而出现的。
5. 类SemValue是在为Token赋值。
6. 抽象类Tree是生成语法树的类的父类。