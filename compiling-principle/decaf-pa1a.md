### 实验pa1a

#### 源代码的结构

1. Lexer.l用以生成Lexer.java文件，作用是把decaf文件的源代码转化为Token的形式，供Parser.java使用，完成的是词法分析的功能。是用正则表达式来实现此功能的。
2. Parser.y用以生成Parser.java文件，作用是把从Lexer.java得到的Token转化为语法树，完成的是语法分析的功能。是用文法规则实现此功能的。
3. 抽象类BaseLexer是作为类Lexer的父类而出现的。
4. 抽象类BasePaser是作为类Paser的父类而出现的。
5. 类SemValue是在为Token赋值。
6. 抽象类Tree：代表抽象语法树的结点。我理解抽象语法树描述的是源代码的**逻辑结构**，我**猜测**每一个叶子结点都是一个**基本数据类型**，每一个非叶子结点都是对基本数据类型的**操作**。抽象类Tree为不同类型的结点设置了约80种属性，比如属性NEWARRAY代表创建新数组，RETURN代表返回状态，IF代表条件语句。这些属性实际上是互不相同的整数。抽象类Tree内设计了近40个它的子类，

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


#### decaf-PA1A的处理过程是什么样的？

1. decaf的总控函数在类Driver.java里，理解了Driver.java里的main函数也就理解了decaf的执行逻辑。
2. 首先会打开源文件，创建目标文件。这部分工作是由Option类的构造函数来完成的。最重要的两个属性是input和output。
3. 具体的分析工作是由Parser类的parseFile方法来完成的。它的作用是生成一颗语法分析树。是采用的自底向上的LALR(1)分析方法。
4. Parser类的parseFile方法就是yyparse()方法，yyparse()方法是由Parser.y自动生成的语法分析工具。
5. yyparse()会调用yylex()方法来获得标记，yylex()方法是由Lexer.l自动生成的词法分析工具。yylex()方法每次只返回一个标记。
6. 把生成的语法分析树写入目标文件中，这是由Tree类的printTo()方法来完成的。至此，decaf-PA1A的处理过程结束。

#### 相同名称的变量出现在源代码不同文件中，它们的含义有什么不同吗？

1. Lexer.l中的标记(NEWLINE、INTEGER等)只是正则表达式的记号，它并未出现在lexer.java中，由此可见lexer.l中的标记对源代码的其它部分无任何影响。lexer.l中用到的另一种标记Parser.XXX只是类Parser的属性。
2.  Parser.y中的标记(由%token定义)是文法分析里的终结符，是在文法规则里用的。在Parser.java中体现为类的属性。而在Parser.y中出现的Tree.XXX虽然名字和Parser.y中的标记相同，但它们的数据类型、值和代表的意义都是不一样的。
3.  Tree.java中的属性(public static final int TOPLEVEL等)虽然有一部分名字和Parser.y中的标记相同，但表达的意义不同，它表示的是抽象语法树里的某种结点。
4.  SemValue.java中即用到了Tree.XXX也用到了Parser.XXX。




































