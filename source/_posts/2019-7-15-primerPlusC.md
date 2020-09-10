---
title: primer plus c笔记（1）
description: primer plus c笔记（1）
categories:
 - C
tags:
 - C
---
## 概览
### 编程机制
#### 目标代码文件、可执行文件和库
C编程的基本策略是使用程序将源代码文件转换为可执行文件，此文件包含可以运行的机器语言代码。C分两步完成这一工作：编译和链接。编译器将源代码转换为中间代码，链接器将此中间代码与其他代码相结合来生成可执行文件。  
中间文件的形式有多种选择。最一般的选择，同时也是我们这里讲述的实现方式所采取的选择，是将源代码转换为机器语言代码，将结果放置在一个目标代码文件（或简称为目标文件）中（这里假定您的源代码由单个文件组成）。虽然目标文件包含机器语言代码，但该文件还不能运行。
目标代码文件中所缺少的第一个元素是一种叫做**启动代码**（start-up code）的东西，此代码相当于您的程序和操作系统之间的接口。
所缺少的第二个元素是库例程的代码。几乎所有C程序都利用标准C库中所包含的例程（称为函数）。
链接器的作用是将这3个元素（目标代码、系统的标准启动代码和库代码）结合在一起，并将它们存放在单个文件，即可执行文件中。对库代码来说，链接器只从库中提取您所使用的函数所需要的代码
![](http://image.zhchy.info/primerC1.jpg)
- - -
## C语言概述
### C语言剖析
![](http://image.zhchy.info/primerC2.jpg)
#### #include指示和头文件
`#include <stdio.h>`  
该语句的作用相当于您在文件中该行所在的位置键入了文件stdio.h的完整内容。实际上，它是一种剪切和粘贴操作，这样可以方便地在多个程序间共享公用的信息。  
#include语句是C预处理器指令（preprocessor directive）的一个例子。通常，C编译器在编译前要对源代码做一些准备工作；这称为预处理（preprocessing）。
stdio.h文件作为所有C编译包的一部分提供，它包含了有关输入和输出函数（例如printf（））的信息以供编译器使用。这个名字代表标准输入输出头文件（standard input/output header）。在C世界中，人们称出现在文件顶部的信息集合为头（header），C实现通常都带有许多头文件。  
最重要的是头文件包括了建立最终的可执行程序时编译器需要用到的信息。例如，它们可以定义常量，或者说明函数名以及该函数如何使用。但是函数的实际代码被包含在一个预编译代码的库文件中，而不是在头文件中。编译器的链接部分负责找到您所需要的库代码。简言之，头文件指引编译器把您的程序正确地组合在一起。  
- - -
#### main()函数
`int main(void)`  
一个C程序总是从main函数开始的，`int`指明了main函数的返回类型  
函数后面的圆括号一般包含传递给函数的信息,`void`表示没有传递任何信息，C90版本支持`main()`的写法，但是在C99版本中不允许
- - -
#### 注释
`/*一个简单的C程序*/`
```c
/* 有效注释  */
/* 两行
   也可以 */
/* 
  这样也可以
*/
/* 这样不行没有结束标记
// C99支持这种风格，不过只能写一行
int rigue // 这样也可以
```
- - -
#### 花括号，程序体和代码块
花括号限定了函数的界限
```c
int main(void) {
  /* some code */
}
```
- - -
#### 声明
`int num`  
传统上C要求必须在代码块开始处生命变量，在C99标准中遵循C++惯例，在使用前声明即可  
变量名可以由字母，数字，下划线组成，并且区分字母大小写，但是必须以字母和下划线开始  
操作系统和C库通常使用以一个或两个下划线开始的名字（例如_kcab），因此您自己最好避免这种用法。标准的标识符通常都以一个或两个下划线开始，例如，库标识符就是这样。这样的标识符都是保留的。这也就是说使用它们虽然不是语法错误，但是这样会导致名字的混乱。  
如果有多个声明可以用逗号分隔  
`int num, num1, num2`
- - -
#### 赋值
`num = 1`  
使用`=`可以将其右边的值赋给左边
- - -
#### printf()函数
```c
init num = 1;
printf("%d is a number", num);
```
`%d`是一个占位符，`％`告诉程序把一个变量在这个位置输出，`d`告诉程序将输出一个十进制（以10为基数）整数变量。`printf（）`函数允许多种输出变量格式，包括十六进制（以16为基数）整数和带小数点的数。实际上，`printf（）`中的f暗示着这是一种格式化（formating）的输出函数。
- - -
#### Return语句
`return 0`  
- - -
### 多个函数
```c
#include <stdio.h>
void butler(void);// 函数原型
// void butler();旧版写法
int main(void)
{
  printf("准备调用butler\n");
  butler();
  printf("调用结束\n");
}
void butler(void)
{
  printf("调用butler\n");
}
```  
- - -
## 数据和C
### 实例程序
```c
#include <stdio.h>
int main(void)
{
  float weight;
  float value;
  printf("输入你的体重:");
  scanf("%f", &weight);
  value = 770 * weight * 14.5833;
  printf("你的体重换算成铑价值$%.2f.\n", value);
  return 0;
}
```
* 上面的代码使用了新的数据类型`float`，这种类型可以处理带小数点的数字
* 打印·`float`类型使用`%f`来处理使用`.2`修饰符可以使浮点数显示小数点后两位
* 使用scanf（）函数为程序提供键盘输入。％f指示scanf（）从键盘读取一个浮点数，&weight指定将输入值赋于名为weight的变量中

- - -
### 变量与常量数据

原来的的关键字|C90关键字|C99关键字
-|-|-
int|signed|_Bool
long|void|_Complex
short|-|_Imaginary
unsigned|-|-
char|-|-
float|-|-
double|-|-

int关键字提供C使用的基本的整数类型。下面3个关键字（long、short和unsigned）以及ANSI附加的signed用于提供基本类型的变种。char关键字用于表示字母以及其他字符（如#、$、％，和*）。char类型也可以表示小的整数。float、double和组合long double表示带有小数点的数。_Bool类型表示布尔值（true和false）。_Complex和_Imaginary分别表示复数和虚数。  
这些类型可以按其在计算机中的存储方式被划分为两个系列，即整数（integer）类型和浮点数（floating-point）类型
- - -
#### 位、字节和字
术语位、字节和字用于描述计算机数据单位或计算机存储单位。这里主要指存储单位。  
最小的存储单位称为位（bit）。它可以容纳两个值（0或1）之一（或者可以称该位被置为关或开）。不能在一个位中存储更多的信息，但是计算机中包含数量极其众多的位。位是计算机存储的基本单位。  
字节（byte）是常用的计算机存储单位。几乎对于所有的机器，1个字节均为8位。这是字节的标准定义，至少在衡量存储单位时是这样（C语言中对此有不同的定义，请参见本章使用字符：char类型小节）。由于每个位或者是0或者是1，所以一个8位的字节包含256 （2的8次方）种可能的0、1组合。这些组合可用于表示0到255的整数或者一组字符。这种表示可以通过二进制编码（仅使用0或1方便地表示数字）来实现  
对于一种给定的计算机设计，字（word）是自然的存储单位。对于8位微机，比如原始的Apple机，一个字正好有8位。使用80286处理器的早期IBM兼容机是16位机，这意味着一个字的大小为16位。基于Pentium的PC机和Macintosh PowerPC中的字是32位。更强大的计算机可以有64位甚至更长位数的字。  
- - -
#### 整数类型与浮点数类型
* 整数  
整数（integer）就是没有小数部分的数。在C中，小数点永远不会出现在整数的书写中。例如2、-23和2456都是整数。数3.14、0.22和2.000都不是整数。整数以二进制数字存储。例如整数7的二进制表示为111，在8位的字节中存储它需要将前5位置0，将后3位置1，如图3.2所示。  
![](http://image.zhchy.info/20190724165741_hR2pQE_primerPlusC-3-2.jpeg)
* 浮点数
浮点数（floating-point）差不多可以和数学中的实数（real number）概念相对应。实数包含了整数之间的那些数。2.75、3.16E7、7.00和2e-8都是浮点数。注意，加了小数点的数是浮点型值，所以7是整数类型，而7.00是浮点型。显然，书写浮点数有多种形式。本书将在后面详细介绍e记数法，这里仅做简要介绍：简单地说，3.16E7表示3.16乘以10的7次方（即1后面带有7个0），7称为10的指数。
这里最重要的一点是浮点数与整数的存储方案不同。浮点数表示法将一个数分为小数部分和指数部分并分别存储。因此尽管7.00和整数7有相同的值，但它们的存储方式不同。与机器中的二进制存储方式相似，在十进制中7.0可表示为0.7E1，这里0.7是小数部分，1是指数部分。图3.3所示为浮点数存储的另一个例子。当然，计算机的内部存储使用二进制数字，它使用2的幂而非10的幂  
![](http://image.zhchy.info/20190724172220_TPYYsI_00577.jpeg)
* 注意  
  * 整数没有小数部分；浮点数可以有小数部分。  
  * 浮点数可以表示比整数范围大得多的数，详见本章结尾表3.4。
  * 对于一些算术运算（例如两个很大的数相减），使用浮点数会损失更多精度。
  * 因为在任何区间内（比如1.0和2.0之间）都存在无穷多个实数，所以计算机浮点数不能表示区域内所有的值。浮点数往往只是实际值的近似。例如，7.0可能以浮点值6.99999存储。稍后我们将讨论更多有关精度的内容。
  * 浮点运算通常比整数运算慢。不过，已经开发出了专门处理浮点运算的微处理器，它可以缩小速度上的差别。

- - -
### C数据类型
#### int类型
指整数，int类型存储在计算机的一个字中，其取值范围与计算机系统有关，最小范围为-32768到32768（8位计算机），一般系统会通过一个指示正负符号的特定位来表示有符号整数。
* 初始化变量
  ```c
  int hogs = 21;
  int cows = 32, goats = 14;
  int dogs, cats = 94;/* 有效但是不好 */
  ```
  最后一行代表声明dogs但是只初始化了cats
![](http://image.zhchy.info/20190724175346_5JFkJu_01646.jpeg)

* 显示八进制和十六进制数  
  要用八进制而不是十进制显示整数，请用％0代替％d。要显示十六进制整数，请使用％x。如果想显示C语言前缀，可以使用说明符％#o%#x和%#X分别生成0、0x和0X前缀。
  ```c
    #include <stdio.h>
    int main(void)
    {
      int x = 100;
      printf("10: %d, 8: %o, 16: %x\n", x, x, x);
      printf("10: %d, 8: %#o, 16: %#x\n", x, x, x);
    }
  ```

* 其他整数类型  
    * short int类型（或者简写为short类型）  
     可能占用比int类型更少的存储空间，用于仅需小数值的场合以节省空间。同int类型一样，short类型是一种有符号类型。
    * long int类型（或者简写为long类型）  
     可能占用比int类型更多的存储空间，用于使用大数值的场合。同int类型一样，long类型是一种有符号类型。
    * long long int类型（或者简写为long long类型（都是在C99标准中引入的）  
     可能占用比long类型更多的存储空间，用于使用更大数值的场合。同int类型一样，long long类型是一种有符号类型。
    * unsigned int类型（或者简写为unsigned类型）    
     用于只使用非负值的场合。这种类型同有符号类型的表示范围不同。例如，16位的unsigned int取值范围为0到65535，而带符号int的取值范围为-32768到32767。由于指示数值正负的位也被用于二进制位，所以无符号数可以表示更大的数值。
    * 在C90 标准中，还允许unsigned long int（简写为unsigned long）和unsigned short int（简写为unsigned short）类型。C99 又增加了unsigned long long int（简写为unsigned long long）类型。
    * 关键字signed可以和任何有符号类型一起使用，它使数据的类型更加明确。例如：short、short int、signed short以及signed short int代表了同一种类型。
* 整数溢出  
当整数溢出时会回到起始点（最小值）
* long常量和long long常量  
如果数值过大编译器会试用unsigned int, long, unsigned long, long long, unsigned long long，如果你希望一个比较小的数字使用long可以通过`12l`或`12L`的方式，对八进制和十六进制同样适用，如果想使用long long可以用`12LL`，如果想使用unsigned long long可以使用`12ull`，`12LLU`或`12Ull`
* 打印short、long、long long、unsigned类型数  

  类型|符号
  -|-
  unsigned int | %u
  long | %ld
  十六进制长整数|%lx
  八进制长整数|%lo
  short|%hd
  八进制short|%ho
  unsigned long|%lu
  long long|%lld
  unsignid long long|%llu

- - -
#### char类型
char类型用于存储字母和标点符号之类的字符。
```c
char broiled; /* 声明一个char变量 */
broiled = 'T'; /* 可以 */
broiled = T; /* 不可以！把T看成一个变量 */
broiled = "T"; /* 不可以！把"T"看成一个字符串 */
```
转义字符

序列|意 义
-|-
\a|警报（ANSIC）
\b|退格
\f|走纸
\n|换行
\r|回车
\t|水平制表符
\V|垂直制表符
\a|警报（ANSIC）
\b|退格
\f|走纸
\n|换行
\r|回车
\t|水平制表符
\V|垂直制表符
`\\`|反斜杠（\）
\'|单引号（'）
\"|双引号（"）
\?|问号（？）
\0oo|八进制值（o表示一个八进制数字）
\xhh|十六进制值（h表示一个十六进制数字）

##### 打印字符
```c
#include <stdio.h>
int main(void)
{
  char ch;
  printf("please enter a character\n");
  scanf("%c", &ch);
  printf("code is %c is %d.\n", ch, ch);
  return 0;
}
```
> scanf（）函数将读取您键入的字符，&符号指示把输入的字符赋给变量ch，接着printf（）函数把ch的值打印两次。首先作为字符打印（由代码中的％c指示），然后作为十进制整数打印（由代码中的％d指示）。注意printf（）说明符决定数据的显示方式而不是决定数据的存储方式，如图所示。  

![](http://image.zhchy.info/20190729151515_CTVtnr_primerPlusC3-6.jpeg)

##### 有符号还是没符号
一些C实现把char当作有符号类型。这意味着char类型值的典型范围为-128到127。另一些C实现把char当作无符号类型，其取值范围为0到255。编译器手册会指明char的类型，或者您可以通过limits.h头文件检查这一信息，下一章将对该头文件做详细介绍。    
根据C90标准，C允许在关键字char前使用signed和unsigned。这样，无论默认的char类型是什么，signed char是有符号类型，而unsigned char则是无符号类型。这对于使用字符类型处理小整数十分有用。如果处理字符，则只须使用不带修饰词的标准char类型。
- - -
#### _Bool类型
_Bool类型由C99引入，用于表示布尔值，即逻辑值true（真）与false（假）。因为C用值1表示true，用值0表示false，所以_Bool类型实际上也是一种整数类型。只是原则上它仅仅需要1位来进行存储。
- - -
#### 可移植的类型：inttypes.h
C99提供了一个可选的名字集合，以确切地描述有关信息。例如：int16_t表示一个16位有符号整数类型，uint32_t表示一个32位无符号整数类型。  
要使这些名字对于程序有效，应当在程序中包含inttypes.h头文件（注意，在编写本书第五版的时候，有些编译器还不支持这个特性）。这个文件使用typedef工具创建了新的类型名字（第5章运算符、表达式和语句中有简要介绍）。比如，该头文件会用uint32_t作为一个具有某种特征的标准类型的同义词或别名，在某个系统中这个标准类型可能是unsigned int，而在另一个系统中则可能是unsigned long。编译器会提供同所在系统相一致的头文件。这些新的名称叫作确切长度类型（exact width type）。注意，与int不同，uint32_t不是关键字，所以必须在程序中包含inttypes.h头文件，编译器才能够识别它。
- - -
#### float,doubule,long double类型
通常，系统使用32位存储一个浮点数。其中8位用于表示指数及其符号，24位用于表示非指数的部分（称为尾数或有效数字）及其符号。  
C还提供一种称为double（意为双精度）的浮点类型。double类型和float类型具有相同的最小取值范围要求，但它必须至少能表示10位有效数字。一般地，double使用64位而不是32位长度。一些系统将多出的32位全部用于尾数部分，这增加了数值的精度并减小了舍入误差。其他的一些系统将其中的一些位分配给指数部分，以容纳更大的指数，从而增加了可以表示的数的范围。每种分配方法都使得数值至少具有13位有效数字，超出了C的最小标准规定。  
C提供了第三种浮点类型long double类型，以满足比double类型更高的精度需求。不过，C只保证long double类型至少同double类型一样精确。  

##### 浮点变量
```c
float noah,jonah;
double trouble;
float planck = 6.63e-34;
long double gnp;
```

##### 浮点常量
```c
-1.56E+12
2.87e-3
3.14159
.2
4e16
.8E-5
100.
1.56 E+12/*这个不对，不要使用空格*/
```  

默认情况下，编译器将浮点常量当作double类型。例如，假设some是一个float变量，您有下面的语句：
```c
some = 4.0 * 2.0;
```
那么4.0和2.0被存储为double类型，（通常）使用64位进行存储。乘积运算使用双精度，结果被截为正常的float长度。  

C使您可以通过f或F后缀使编译器把浮点常量当作float类型，比如2.3f和9.11E9F。1或L后缀使一个数字成为long double类型，比如54.31和4.32e4L。建议使用L后缀，因为字母l和数字1容易混淆。没有后缀的浮点常量为double类型。

##### 打印浮点值
printf（）函数使用％f格式说明符打印十进制记数法的float和double数字，用%e打印指数记数法的数字。如果系统支持C99的十六进制格式浮点数，您可以使用a或A代替e或E。打印long double类型需要％Lf、％Le和％La说明符。注意float和double类型的输出都使用％f、％e或％a说明符。  

##### 浮点值的上溢和下溢
当计算结果是一个大得不能表达的数时，会发生上溢。对这种情况的反应原来没有规定，但是现在的C语言要求为toobig赋予一个代表无穷大的特殊值，printf（）函数显示此值为inf或infinity（或这个含义的其他名称）。  
当除以一个十分小的数时，情况更复杂一些。回忆一下，float数字被分为指数和尾数部分进行存储。有这样的一个数，它具有最小的指数，并且仍具有可以由全部可用位进行表示的最小的尾数值。这将是能用对浮点值可用的全部精度进行表示的最小数字。现在把此数除以2。通常这个操作将使指数部分减小，但是指数已经达到了最小值；所以计算机只好将尾数部分的位进行右移，空出首位二进制位，并丟弃最后一位二进制值。以十进制为例，把一个包含四位有效数字的数0.1234E-10除以10，将得到结果0.0123E-10,但是损失了一位有效数字。此过程称为下溢（underflow）。C将损失了类型精度的浮点值称为低于正常的（subnormal），所以把最小的正浮点数除以2将得到一个低于正常的值。如果除以一个足够大的值，将使所有的位都为0。现在C库提供了用于检查计算是否会产生低于正常的值的函数。  
还有另外一个特殊的浮点值NaN（Not-a-Number）。例如asin（）函数返回反正弦值，但是正弦值不能大于1，所以asin（）函数的输入参数不能大于1，否则函数返回NaN值，printf（）函数将此值显示为nan, NaN或类似形式。  

##### 复数和虚数类型
很多科学和工程计算需要复数和虚数。C99标准支持这些类型，但是有所保留。一些自由实现中不需要这些类型，比如一些嵌入式处理器的实现（VCR芯片就不需要复数）。同样的，虚数类型也是可选的。
简单地讲，有3种复数类型，分别是float_Complex、double_Complex和long double_Complex。float_Complex变量包含两个float值，一个表示复数的实部，另一个表示复数的虚部。与之类似，有3种虚数类型，分别是float _Imaginary、double _Imaginary和long double _Imaginary。  
如果您包含了complex.h头文件，则您可以用complex代替_Complex，用imaginary代替_Imaginary,用符号I表示-1的平方根。
- - -
#### 类型大小
C的内置运算符sizeof以字节为单位给出类型的大小。为打印sizeof数值，一些编译器要求用%lu代替％u，这是因为C允许由具体的实现来选择sizeof返回的结果值实际使用哪种无符号整数类型。C99为此提供了％zd说明符，如果编译器支持，可以考虑使用该说明符。
```c
#include <stdio.h>

int main(void)
{
  printf("size is %zd\n", sizeof(int));// 4
  printf("size is %lu\n", sizeof(char));// 1
  printf("size is %u\n", sizeof(long));// 8
  printf("size is %u\n", sizeof(double));// 8
  return 0;
}
```
- - -
## 字符串和格式化输入/输出
### 前导程序
```c
#include <stdio.h>
#include <string.h>  // 提供strlen()函数的原型
#define DENSITY 62.4 // 人的密度（单位：英镑/每立方尺)
int main(void)
{
  float weight, volume;
  int size, letters;
  char name[40]; //name是一个有40个字符的数组

  printf("hi! what's your first name?\n");
  scanf("%s", name);
  printf("%s, what's your weight?\n", name);
  scanf("%f", &weight);
  size = sizeof name;
  letters = strlen(name);
  volume = weight / DENSITY;
  printf("%s, your volume is %2.2f cubic feet.\n", name, volume);
  printf("your first name has %d letters\n", letters);
  printf("we have %d bytes to store it\n", size);
  return 0;
}
```
> 使用数组存放字符串，该数组是内存中连续的40个字节，每个字节都能存放一个字符值  
> 使用·`%s`来输出输入字符串*在`scanf()`中`weight`使用了&前缀而name没有使用  
> 使用了C预处理器定义了代表62.4的符号常量DENSITY  
> 使用`strlen()`来获取字符串长度  

- - -
### 字符串简介
字符串（character string）就是一个或多个字符的序列。
#### char数组类型和空字符
C没有为字符串定义专门的变量类型，而是把它存储在char数组中。字符串中的字符存放在相邻的存储单元中，每个字符占用一个单元；而数组由相邻存储单元组成，所以把字符串存储到数组中是很自然的
![](http://image.zhchy.info/20190802170257_CfON6G_primerPlusC-4-1.jpeg)
上面的`\0`用来标记字符串的结束
- - -
#### 使用字符串
```c
#include <stdio.h>
#define PRAISE "what a super marvelous name!"
int main(void)
{
  char name[40];

  printf("what's your name?\n");
  scanf("%s", name);
  printf("hello, %s.%s\n", name, PRAISE);
  return 0;
}
```
scanf（）只读取了Hilary Bubbles的名字Hilary。scanf（）开始读取输入以后，会在遇到的第一个空白字符空格（blank）、制表符（tab）或者换行符（newline）处停止读取。因此，它在遇到Hilary和Bubbles之间的空格时，就停止了扫描。一般情况下，使用％s的scanf（）只会把一个单词而不是把整个语句作为字符串读入。C使用其他读取输入函数（例如gets（））来处理一般的字符串。  

**字符与字符串**
字符串常量x与字符常量x不同。其中一个区别是‘x’属于基本类型（char），而x则属于派生类型（char数组）。第二个区别是x实际上由两个字符（‘x’和空字符‘\0’）组成
![](http://image.zhchy.info/20190802193422_sl6VHp_primerPlusC-4-3.jpeg)
- - -
#### strlen()函数
`strlen()`函数会返回字符串的长度
```c
#include <stdio.h>
#include <string.h> //提供strlen()原型
#define PRAISE "what a super marvelous name!"
int main(void)
{
  char name[40];

  printf("what's your name?\n");
  scanf("%s", name);
  printf("hello, %s.%s\n", name, PRAISE);
  printf("your name of %d letters occupies %d memory cells.\n", strlen(name), sizeof(name));
  printf("the phrase of praise has %d letters", strlen(PRAISE));
  printf("and occupies %d memory cells.\n", sizeof PRAISE);
  return 0;
}
```
根据sizeof运算符的报告，数组name有40个内存单元。不过只用了其中前6个单元来存放Morgan，这是strlen（）所报告的。数组name的第7个单元中放置空字符，它的存在告诉strlen（）在哪里停止计数。
![](http://image.zhchy.info/20190805143813_0IsPDS_primerPlusC-4-4.jpeg)
对于PRAISE，您会发现strlen（）再一次给出了字符串中字符（包括空格和标点符号）的准确数目。Sizeof运算符提供给您的数目比前者大1，这是因为它把用来标志字符串结束的不可见的空字符也计算在内。  
在使用sizeof时对于类型`()`是必须的，但是对于具体量是可选的，但是推荐在所有情况下都是用`()`
- - -
### 常量和C预处理器
使用`#define TAXRATE 0.015`可定义一个名字为TAXRATE值为0.015的常量   
当编译程序时，值0.015会在TAXRATE出现的每个地方替换它
- - -
#### const修饰符
C90添加了一种创建符号常量的第二种方法，即可以使用const关键字把一个变量声明转换为常量声明：
```c
const int MONTHS = 12;// MONTHS是一个代表12的符号常量
```
- - -
### 研究如利用printf()和scanf()
#### printf()函数
请求printf（）打印变量的指令取决于变量的类型。例如，在打印整数时使用％d符号，而在打印字符时使用％c符号。这些符号被称为转换说明（conversion specification），因为它们指定了如何把数据转换成可显示的形式。  

转换说明|输出
-|-
%a|浮点数、十六进制数字和p-记数法（C99)
%A|浮点数、十六进制数字和P-记数法（C99)
%c|一个字符
%d|有符号十进制整数
%e|浮点数、e-记数法
%E|浮点数、E-记数法
%f|浮点数、十进制记数法
%g|根据数值不同自动选择％f或％e。%e格式在指数小于-4或者大于等于精度时使用
%G|根据数值不同自动选择％f或％E。％E格式在指数小于-4或者大于等于精度时使用
%i|有符号十进制整数（与％d相同）
%o|无符号八进制整数
%p|指针
%s|字符串
%u|无符号十进制整数
%x|使用十六进制数字0f的无符号十六进制整数
%X|使用十六进制数字0F的无符号十六进制整数
%%|打印一个百分号  

- - -
##### 使用printf()
```c
#include <stdio.h>
#define PI 3.141593

int main(void)
{
  int number = 5;
  float expresso = 13.5;
  int cost = 3100;
  printf("the %d ceos drank %f cups of expresso.\n", number, expresso);
  printf("the value of pi is %f\n", PI);
  printf("farewell! thou art too dear for my possessing, \n");
  printf("%c%d\n", '$', 2 * cost);
}
```
![](http://image.zhchy.info/20190805161626_6o5v3R_primerPlusC-4-6.jpeg)  
![](http://image.zhchy.info/20190805161650_WsjBG8_primerPlusC-4-7.jpeg)
- - -
##### printf()的转换说明修饰符
可以在％和定义转换字符之间通过插入修饰符对基本的转换说明加以修改。

修饰符|意义
-|-
标志|五种标志（-、+、空格、#和0）都将在表4.5中描述。可以使用零个或者多个标志
digit（s）|字段宽度的最小值。如果该字段不能容纳要打印的数或者字符串，系统就会使用更宽的字段,示例：“％4d”
.digit（s）|精度。对于％e、％E和％f转换，是将要在小数点的右边打印的数字的位数。对于％g和％G转换，是有效数字的最大位数。对于％s转换，是将要打印的字符的最大数目。对于整数转换，是将要打印的数字的最小位数；如果必要，要使用前导零来达到这个位数。只使用“.”表示其后跟随一个零，所以％.f与％.0f相同,示例；“％5.2f”打印一个浮点数，它的字段宽度为5个字符，小数点后有两个数字
h|和整数转换说明符一起使用，表示一个short int或unsigned short int类型数值,示例：“%hu”、“%hx”和“％6.4hd”
hh|和整数转换说明符一起使用，表示一个signed char或unsigned char类型数值,示例：“%hhu”、“%hhx”和“％6.4hhd”
j|和整数转换说明符一起使用，表示一个intmax_t或uintmax_t值,示例：“%jd”和“％8jX”
l|和整数转换说明符一起使用，表示一个long int或unsigned long int类型值,示例：“％ld”和“％81u”
ll|和整数转换说明符一起使用，表示一个long long int或unsigned long long int类型值（C99）,示例；“％lld”和“％8llu”
L|和浮点转换说明符一起使用，表示一个long double值,示例；“%Lf”和“％10.4Le”
t|和整数转换说明符一起使用，表示一个ptrdiff_t值（与两个指针之间的差相对应的类型）（C99）,示例：“％td”和“％12ti”
z|和整数转换说明符一起使用，表示一个size_t值（sizeof返回的类型）（C99）,示例：“％zd”和“％12zx”

标 志|意 义
-|-
-|项目是左对齐的；也就是说，会把项目打印在字段的左侧开始处,示例：“％-20s”
+|有符号的值若为正，则显示带加号的符号；若为负，则带减号的符号,示例：“％+6.2f”
(空格)|有符号的值若为正，则显示时带前导空格（但是不显示符号）；若为负，则带减号符号。+标志会覆盖空格标志,示例：“％6.2f”
`#`|使用转换说明的可选形式。若为％o格式，则以0开始；若为%x和%X格式，则以0x或0X开始。对于所有的浮点形式，#保证了即使不跟任何数字，也打印一个小数点字符。对于%g和％G格式，它防止尾随零被删除,示例：“％#o”、“％#8.0f”和“％+#10.3E”
0|对于所有的数字格式，用前导零而不是用空格填充字段宽度。如果出现-标志或者指定了精度（对于整数）则忽略该标志,示例：“％010d”和“％08.3f”  

示例  
```c
#include <stdio.h>
#define PAGES 931
int main(void)
{
  printf("*%d*\n", PAGES); // *931*
  printf("*%2d*\n", PAGES); // *931*
  printf("*%10d*\n", PAGES); // *       931*
  printf("*%-10d*\n", PAGES); // *931       *
  return 0;
}
```  
```c
#include <stdio.h>

int main(void)
{
  const double RENT = 3852.99;

  printf("*%f*\n", RENT); // *3852.990000*
  printf("*%e*\n", RENT); // *3.852990e+03*
  printf("*%4.2f*\n", RENT); // *3852.99*
  printf("*%3.1f*\n", RENT); // *3853.0*
  printf("*%10.3f*\n", RENT); // *  3852.990*
  printf("*%10.3e*\n", RENT); // * 3.853e+03*
  printf("*%+4.2f*\n", RENT); // *+3852.99*
  printf("*%010.2f*\n", RENT); // *0003852.99*
  return 0;
}
```

```c
#include <stdio.h>
int main(void)
{
  printf("%x %X %#x\n", 31, 31, 31); // 1f 1F 0x1f
  printf("**%d**% d**% d**\n", 42, 42, -42); // **42** 42**-42**
  printf("**%5d**%5.3d**%05d**%05.3d**\n", 6, 6, 6, 6); // **    6**  006**00006**  006**
  return 0;
}
```
```c
#include <stdio.h>
#define DLURB "Authentic imitation! "
int main(void)
{
  printf("/%2s/\n", DLURB); // /Authentic imitation! /
  printf("/%24s/\n", DLURB); // /   Authentic imitation! /
  printf("/%24.5s/\n", DLURB); // /                   Authe/
  printf("/%-24.5s/\n", DLURB); // /Authe                   /
  return 0;
}
```
- - -
#### 转换说明的意义
它把存储在计算机中的二进制格式的数值转换成一系列字符（一个字符串）以便于显示
不匹配的情况


