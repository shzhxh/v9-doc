### 实验pa1a

#### 源代码的结构

1. Lexer.l用以生成Lexer.java文件，作用是把decaf文件的源代码转化为Token的形式，供Parser.java使用，完成的是词法分析的功能。是用正则表达式来实现此功能的。
2. Parser.y用以生成Parser.java文件，作用是把从Lexer.java得到的Token转化为语法树，完成的是语法分析的功能。是用文法规则实现此功能的。
3. 抽象类BaseLexer是作为类Lexer的父类而出现的。
4. 抽象类BasePaser是作为类Paser的父类而出现的。
5. 类SemValue是在为Token赋值。
6. 抽象类Tree是生成语法树的类的父类。

#### 跟踪注释的处理过程
1. 依据正则表达式("//"[^\r\n]*{NEWLINE})将注释匹配为S_COMMENT
2. 依据注释的规则 { /* Just ignore */ }，什么都不做，注释处理完毕

#### 跟踪关键字的处理过程
1. 是直接匹配和关键字对应的字符串，关键字对应的规则均为{ return keyword(Parser.XXX); }
2. Parser.XXX的XXX是在Parser.y文件中定义的标记(token)，通过观察Parser.java文件可见它实际上是个序号唯一的数字
3. keyword方法的作用是为Parser对象的yylval属性赋值，并返回Parser.XXX的值。
4. yylval是一个SemValue对象，是为它的loc和code属性赋值，code就是Parser.XXX的值，loc是一个Location对象。
5. loc有两个属性，lin代表行号，col代表列号。
6. 到这里我觉得有点奇怪，目前编译器应该是输入decaf源文件，输出输出类似Testcase/S1/result/*.result之类的文件，但从以上分析看不出有输出什么到文件。我想是不是应该看看总控函数public static void main做了些什么。
7. 通过grep命令找到main函数在Driver.java文件中。main函数只有4条指令，它创建了一个Driver对象，对Driver对象的option属性赋值，调用init方法，调用compile方法。
8. option属性是一个Option对象，在赋值的时候是通过读取命令参数来指定Option对象的属性srcFileName,dstFileName,input,output。
9. init方法创建了Lexer对象，Parser对象，DecafError对象。
10. compile方法首先使用parser.parseFile方法解析输入并执行指示项，然后使用checkPoint方法找到解析错误的地方，最后使用条件判断做相应的处理。
11. 通过总控函数知道了大概的过程，但还是很迷惑，因为对象之间的关系也很复杂。
12. 应该怎么样继续理解编译器里这些对象呢？从整体来看其实有三个对象：decaf源文件，编译器，decaf目标文件。编译器里分了这么多对象，其实目标只有一个，就是把源文件转换成目标文件。我想从源文件和目标文件这个角度来理解编译器的工作原理是不是更容易一点呢？
13. 源文件在Driver对象的option属性里，option属性本身也是Opiton对象，源文件具体对应着的是srcFileName属性。
14. srcFileName被转化成了InputStream对象input，我猜测就是把源文件载入内存，并把内存中的文件抽象成字节流。
15. input传递给了Lexer对象lexer,lexer和parser绑定对应关系。
16. parser.yyparse方法进行了具体的分析工作。依据Yacc的说明文档，yyparse又调用了yylex进行词法分析的工作，得到标记(tokens)。如果一切正常，yyparse会返回0,否则返回1。







































