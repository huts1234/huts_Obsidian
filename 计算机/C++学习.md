# C++基础入门
---
## 1. C++初识
---
### 1.1第一个C++程序

```
#include <iostream>
using namspace std;
int main()
{
   
    cout << "Hello cpp!" << endl;//这是一行输出语句
    return 0;
}
```
### 1.2 注释
定义：用于解释代码含义，不参与代码运行
注释的两种类型：
1. 单行注释：`\\ 单行注释内容`
2. 多行注释：
```
/*
多行注释内容
*/
```
### 1.3 变量
作用： 给一段指定的的内存空间起名，方便操作这段内存
语法： `数据类型 变量名 = 初始值;`
实例：

```
#include <iostream>

using namespace std;

int main()
{
    int a = 10;
    cout<<"a = "<< a <<endl;
    cout << "Hello cpp!" << endl;//这是一行输出语句
    return 0;
}
```
### 1.4 常量
作用： 用于记录程序中不可更改的数据
C++定义常量的两种方式
1. “#define 宏常量”： `#define 常量名 常量值`
	- 通常在文件上方定义，表示一个常量
2. const 修饰的变量 `const 数据类型 常量名 = 常量值`
代码示例：
```
#define day 7
#include <iostream>
using namespace std;
int main()
 {
     // day = 14; 错误 day 为常量，不可修改
     cout<<"一周总共有："<<day<<"天"<<endl;
     const int month = 12;
     //month =24 错误,const 修饰的变量也是常量
     cout<<"一年总共有："<<month<<"个月份"<<endl;
     return 0;
}
```
### 1.5 关键字
**作用** ：关键字是C++中预先保留的单词（标识符）
- **在定义变量或者常量的时候，不要用关键字**
C++的关键字如下：

| asm        | do           | if               | return      | typedef  |
| ---------- | ------------ | ---------------- | ----------- | -------- |
| auto       | double       | inline           | short       | typeid   |
| bool       | dynamic_cast | int（数据类型：整型）     | sighed      | typename |
| break      | elese        | long             | sizeof      | union    |
| case       | enum         | mutable          | static      | unsighed |
| catch      | expliclt     | namespace        | static_cast | using    |
| char       | export       | new              | struct      | virtual  |
| class      | extern       | operator         | switch      | void     |
| const      | false        | private          | template    | volatile |
| const_cast | float        | protected        | this        | wchar_t  |
| continue   | for          | public           | throw       | while    |
| default    | friend       | register         | true        |          |
| delete     | goto         | reinterpret_cast | try         |          |
`提示：在给变量或常量起名的时候，不要用C++关键字，否则会产生歧义。`
```
#include <iostream>
using namespace std;
int main()
{
    //创建变量：数据类型 变量名 = 变量初始值
    //不要用关键字给变量或常量命名
    //int int = 10; 错误，第二个int是关键字。不可以作为变量名称
    
    system("pause");
    
    return 0;
}
```


### 1.6 标识符命名规则
**作用**：C++规定给标识符（变量，常量）命名时，有一套自己的规则
- 标识符不能是关键字
- 标识符只能由字母、数字、下划线组成
- 第一个字符必须为字母或下滑线
- 标识符中字母区分大小写
`建议：给标识符命时，争取做到见名知意的效果，方便自己和他人阅读`
```

```

## 2. 数据类型
C++规定在创建一个变量或者常量时，必须要指定出相应的数据类型，否则无法给变量分配内存
- - -
### 2.1 整型
**作用**：整型变量表示的是===整数类型===的数据
C++中能够表示整型的类型有以下集中方式，**区别在于所占内存空间不同**：



| 数据类型             | 占用空间                                | 取值范围           |
| ---------------- | ----------------------------------- | -------------- |
| short(短整型)       | 2字节                                 | (-2^15~2^15-1) |
| int（整型）          | 4字节                                 | (-2^31~2^31-1) |
| long（长整型）        | windows为4字节,linux为4字节(32位),8字节（64位） | (-2^31~2^31-1) |
| long long （长长整型） | 8字节                                 | (-2^63~2^63-1) |
```

#include <iostream>
using namespace std;
int main()
 {
     //短整型（-32768～32767）
    short num1 = 10;
     //整型
    int num2 = 10;
     //长整型
    long num3 = 10;
     //长长整型
     long long num4 = 10;
     cout<< "num1 ="<<num1<<endl;
     cout<<"num2 ="<<num2<<endl;
     cout<<"num3 ="<<num3<<endl;
     cout<<"num4 ="<<num4<<endl;
     return 0;
} 
```
### 2.2 sizeof关键字
**作用**：利用sizeof关键字可以===统计数据类型所占内存的大小===
**语法：** `sizeof（数据类型/变量）`
**实例：**
```
#include <iostream>
using namespace std;
int main()
 {
     cout<< "short 类型所占内存空间为："<< sizeof(short)<<endl;
     cout<< "int 类型所占内存空间为："<< sizeof(int)<<endl;
     cout<< "long 类型所占内存空间为："<< sizeof(long)<<endl;
     cout<< "long long 类型所占内存空间为："<< sizeof(long long)<<endl;
     return 0;
} 
```
### 2.3 实型（浮点型）
**作用**： 用于===表示小数===
浮点型变量分为两种：
1. 单精度float
2. 双精度double
两者的区别在于表示的有效数字范围不同。

`科学计数法：aen=a*10^n`

| 数据类型   | 占用空间 | 有效数字范围     |
| ------ | ---- | ---------- |
| float  | 4字节  | 7位有效数字     |
| double | 8字节  | 15～16位有效数字 |
```
#include <iostream>
using namespace std;
int main()
 {
     float f1 = 3.1415926f;//默认情况下编译器会将小数当作双精度，因此单精度要加f
     double d1 = 3.1415936;
     cout << "f1 ="<<f1 <<endl;
     cout << "d1 ="<< d1<< endl;
     //在C++的编译器中，输出结果默认最多保留小数的6位有效数字
     cout<<"float 所占的内存空间为："<<sizeof(float)<<endl;  //输出为4
     cout<<"double 所占的内存空间为："<<sizeof(double)<<endl;//输出为8
     
     //科学计数法
     float f2 = 3e2;//3*10^2
     cout << "f2 = "<<f2<<endl;
     float f3 = 3e-2;//3*0.1^2
      cout << "f3 = "<<f3<<endl;
     return 0;
} 
```
### 2.4 字符型
**作用：** 字符型变量用于显示单个字符
**语法:**      `char ch = 'a';` 
- 注意1：在显示字符型变量时，用===单引号===将字符括起来，不要===用双引号===
- 注意2：单引号内只能有===一个字符===，不可以是==字符串==

- C和C++中字符型变量只占用===1个字节===。
- 字符型变量并不是把字符本身放到内存中储存，而是将对应的ASCII编码放入到储存单元。
```
#include <iostream>
using namespace std;
int main()
 {
     //1.字符型变量创建方式
     char ch = 'B';
     cout <<"ch 为："<<ch<<endl;
     //2.字符型变量所占内存大小
     cout << "char 所占的空间大小为"<<sizeof(char)<<endl;
/*
      字符型变量常见错误
      ch  = "a"; # 错误，不可以用双引号
      ch = 'abcde'; # 错误，单引号内只能引用一个字符
*/
	// 4.字符型变量的ASCII编码
	 //A -- 65
     //a -- 97
     cout << (int)ch << endl;
     
     return 0;
} 
```
ASCII码表格：

| ASCII值 | 控制字符 | ASCII值 | 字符      | ASCII值 | 字符  | ASCII值 | 字符  |
| ------ | ---- | ------ | ------- | ------ | --- | ------ | --- |
| 0      | NUT  | 32     | (space) | 64     | @   | 96     | 、   |
| 1      | SOH  | 33     | !       | 65     | A   | 97     | a   |
| 2      | STX  | 34     | "       | 66     | B   | 98     | b   |
| 3      | ETX  | 35     | #       | 67     | C   | 99     | c   |
| 4      | EOT  | 36     | $       | 68     | D   | 100    | d   |
| 5      | ENQ  | 37     | %       | 69     | E   | 101    | e   |
| 6      | ACK  | 38     | &       | 70     | F   | 102    | f   |
| 7      | BEL  | 39     | ,       | 71     | G   | 103    | g   |
| 8      | BS   | 40     | (       | 72     | H   | 104    | h   |
| 9      | HT   | 41     | )       | 73     | I   | 105    | i   |
| 10     | LF   | 42     | *       | 74     | J   | 106    | j   |
| 11     | VT   | 43     | +       | 75     | K   | 107    | k   |
| 12     | EF   | 44     | ''      | 76     | L   | 108    | l   |
| 13     | CR   | 45     | -       | 77     | M   | 109    | m   |
| 14     | SO   | 46     | .       | 78     | N   | 110    | n   |
| 15     | SI   | 47     | /       | 79     | O   | 111    | o   |
| 16     | DLE  | 48     | 0       | 80     | P   | 112    | p   |
| 17     | DC1  | 49     | 1       | 81     | Q   | 113    | q   |
| 18     | DC2  | 50     | 2       | 82     | R   | 114    | r   |
| 19     | DC3  | 51     | 3       | 83     | S   | 115    | s   |
| 20     | DC4  | 52     | 4       | 84     | T   | 116    | t   |
| 21     | NAK  | 53     | 5       | 85     | U   | 117    | u   |
| 22     | SYN  | 54     | 6       | 86     | V   | 118    | v   |
| 23     | TB   | 55     | 7       | 87     | W   | 119    | w   |
| 24     | CAN  | 56     | 8       | 88     | X   | 120    | x   |
| 25     | EM   | 57     | 9       | 89     | Y   | 121    | y   |
| 26     | SUB  | 58     | :       | 90     | Z   | 122    | z   |
| 27     | ESC  | 59     | ;       | 91     | [   | 123    | {   |
| 28     | FS   | 60     | <       | 92     | /   | 124    | \|  |
| 29     | GS   | 61     | =       | 93     | ]   | 125    | }   |
| 30     | RS   | 62     | >       | 94     | ^   | 126    | `   |
| 31     | US   | 63     | ?       | 95     | _   | 127    | DEL |
ASCII 码大致分为两部分：
- ASCII非打印控制字符：ASCII表上的数字0 - 31分配给了控制字符，用于控制像打印机等一些外围设备。
- ASCII打印字符：数字32-126分配给能在键盘上找到的字符，当查看或打印文档时就会出现。
### 2.5 转义字符
**作用：** 用于表示一些===不能显示出来的ASCII字符=== 

| 转义字符 | 含义                   | ASCII码值（十进制） |
| ---- | -------------------- | ------------ |
| \a   | 警报                   | 007          |
| \b   | 退格（BS），将当前位置移到前一列    | 008          |
| \f   | 换页（FF），将当前位置移到下页开头   | 012          |
| \n   | 换行（LF），将当前位置移到下一行开头  | 010          |
| \r   | 回车（CR），将当前位置移到本行开头   | 013          |
| \t   | 水平制表（HT）（跳到下一个TAB位置） | 009          |
| \v   | 垂直制表（VT）             | 011          |
| \\\  | 代表一个反斜杠字符“\”         | 092          |
| \\'  | 代表一个单引号字符            | 039          |
| \\"  | 代表一个双引号字符            | 034          |
| \\?  | 代表一个问号               | 063          |
| \0   | 数字0                  | 000          |
| \ddd | 8进制转义字符，d的范围0-7      | 3位8进制        |
| \xhh | 16进制转义字符，h的范围        | 3位16进制       |
```
#include <iostream>
using namespace std;
int main()
 {
     cout << "hello world" << endl;
     cout << "hello world\n" << endl;
     cout << "\\" << endl;
     cout << "aa \t helloworld" << endl;
     cout << "aaa \t helloworld" << endl;
     cout << "aaaa \t helloworld" << endl;
     
     return 0;
} 
```
### 2.6 字符串型
**作用：** 用于表示一串字符
**两种风格** 
1. **C风格字符串：** `char 变量名[] = "字符串值"`
**实例：** 
```
#include <iostream>
using namespace std;
int main()
 {
     char str1[] = "hello world";
     cout << str1 << endl;
     
     
     return 0;
} 
```
- 注意：C风格的字符串要用双引号括起来。
2. **C++风格字符串：** `string 变量名 = "字符串值"`
**实例：**
```
#include <iostream>
using namespace std;
#include <string> //用C++风格的字符串的时候，要包含这个头文件 
int main()
 {
     string str = "hello world";
     cout << str << endl;
      
     
     return 0;
} 
```

### 2.7 布尔类型bool
**作用：** 布尔数据类型代表真或假的值
bool类型只有两个值：
- true  ---真（本质是1）
- false ---假（本质是0）
bool类型占===1个字节===大小
实例:
```
#include <iostream>
using namespace std;
int main()
 {
    bool flag = true;//true代表真
     cout << flag << endl;//1
     
     flag = false;//false代表假
     cout << flag << endl;//0
     
     cout << "size of bool = " << sizeof(bool) << endl;//1
     
     return 0;
}
```

### 2.8 数据的输入
**作用：** 用于从键盘获取
**关键字：** cin
**语法：** `cin >> 变量`
实例：
```cpp
#include <iostream>

using namespace std;

int main()

{

//整型输入

int a = 0;

cout << "请输入整型变量："<< endl ;

cin >> a;

cout << a << endl;

  

// 浮点型输入

double d = 0;

cout << "请输入浮点型变量：" << endl ;

cin >> d ;

cout << d << endl;

  

//字符型输入

char ch = '0';

cout << "请输入字符型变量：" << endl ;

cin >> ch;

cout << ch << endl;

  

//字符串型输入

string str = "zd";

cout << "请输入字符串变量：" << endl;

cin >> str;

cout << str << endl;


// bool数据类型

bool flag = false;

cout << "请给布尔类型flage赋值" << flag <<endl ;

cin >> flag ;

cout << "布尔类型flag = " << flag << endl ;
  

return 0;

}
```


## 3.运算符

 **作用：** 用于执行代码的运算。


| **运算符类型** | 作用                  |
| --------- | ------------------- |
| 算数运算符     | 用于处理四则运算            |
| 赋值运算符     | 用于将表达式的值赋给变量        |
| 比较运算符     | 用于表达式的比较，并返回一个真值或假值 |
| 逻辑运算符     | 用于根据表达式的值返回真值或假值    |

--- 
### 3.1 算术运算符
**作用：** 用于四则运算
算数运算符包括以下符号：

| 运算符 | 术语     | 实例         | 结果      |
| --- | ------ | ---------- | ------- |
| +   | 正号     | +3         | 3       |
| -   | 负号     | -3         | -3      |
| +   | 加      | 10+5       | 15      |
| -   | 减      | 10-5       | 5       |
| *   | 乘      | 10*5       | 50      |
| /   | 除      | 10/5       | 2       |
| %   | 取模（取余） | 10%3       | 1       |
| ++  | 前置递增   | a=2;b=++a; | a=3;b=3 |
| ++  | 后置递增   | a=2;b=a++; | a=3;b=2 |
| --  | 前置递减   | a=2;b=--a  | a=1;b=1 |
| --  | 后置递减   | a=2;b=a--  | a=1;b=2 |
**实例：**
```cpp
#include <iostream>  
using namespace std;  
int main() {  
  
    //加减乘除  
    int a1 = 10;  
    int b1 = 3;  
    cout << a1 + b1 << endl;//13  
    cout << a1 - b1 << endl;//7  
    cout << a1 * b1 << endl;//30  
    cout << a1 / b1 << endl;//3  
    cout << a1 % b1 << endl;//1  
	//注:两个整数相除，结果依然是整数，将小数部分去除；两个数相处，除数不能为0  
    int a2 = 10;  
    int b2 = 20;  
    cout << a2 / b2 << endl;  
	//两个小数可以相除  
    double d1 = 0.5;  
    double d2 = 0.22;  
    cout << "d1 /d2 = " << d1 / d2<< endl;  
  
    int a1 = 10;  
    int b1 = 3;  
	cout << a1 % b1 << endl;  
  
	int a2 = 10 ;  
	int b2 = 20;  
	cout << a2 % b2 << endl;
	// 前置递增  
	int a = 10;  
	++a;  
	cout<<a<<endl;  
	// 后置递增  
	int b = 10;  
	b ++;  
	cout<<b<<endl;  
	// 前置和后置的区别  
	//前置递增 先让变量+1 然后进行表达式运算  
	int a2 = 10;  
	int b2 = ++a2*10;  
	cout<<a2<<endl;  
	cout<<b2<<endl;  
	//后置递增 先进行表达式运算 后让变量+1  
	int a3 = 10;  
	int b3 = a3++ * 10;  
	cout<<a3<<endl;  
	cout<<b3<<endl;
    return 0;  
}
```
- 除法&取模运算中除数不能为0
- 两个小数不能做取模运算
### 3.2 赋值运算符
### 3.3 比较运算符
### 3.4 逻辑运算符

## 4.程序流程结构
## 5.数组
## 6. 函数
## 7. 指针
## 8.结构体

