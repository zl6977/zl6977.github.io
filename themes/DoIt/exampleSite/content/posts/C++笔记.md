---
aliases: null
date: 2022-04-07 12:11:00 +02:00
tags: [2022-04]
title: C++笔记
---

#程序员 
- [x] Essential C++ #someday ✅ 2022-04-18
# 1 编程基础
很快就看完了，其中的文件操作不是很熟练，但应该不难。

# 2 面向过程的编程风格。
## 指针(pointer)与引用(reference)
pointer参数和reference参数二者之间功能类似，用法略有不同，更重要的差异是：
- pointer 可能为空，不指向实际对象。当我们提领pointer时，一定要先确定其值并非为0。
- 而对于reference，其必然指向某个对象，所以不须做此检查。

若需要更改原变量，需要传址，以下两者皆可：
function(int& inPara);
function(int* inParaPt);
若不需要更改原变量，只需传值，以下两者皆可：
function(int inPara);
function(const int& inPara); //此方法不需要在内存中复制一份原变量。
- 如果是内建变量，因为内存占用很少，两者皆可。
- 如果是自定义类，一般用后者，省时省空间。

## 内存管理
- file extent. This is not a good choice in most cases.
- local scope
- dynamic extent
    - new, delete
    - exist in heap memory
    - if not deleted, ==memory leak== happens.

### 动静态内存分配
```C++
//见bilibili收藏
//静态内存分配，在stack，由编译器自动分配内存，生命周期结束自动释放
int i = 10;
//动态内存分配，在heap，如果不delete会造成内存溢出
int *j = new int(20);
delete j;
```

## 静态局部对象  local static objects
只对该函数可见的静态变量。
可以避免一些重复计算。

## inline函数
写在函数内的函数，既满足复用，又加快运行速度。
inline的定义(实现代码)常常写在头文件中？

## 头文件中的声明，extern关键字
这并不很正确，因为它会被解读为seq_array的定义而非声明，就像函数一样，一个对象只能在程序中被定义一次。
对象的定义就像函数定义一样，必须置于程序代码文件。只要在上述seq_array定义式前加上关键词extern，它便成为了一个声明：
```c++
const int seq_cnt = 6;
//OK：以下是一个声明
extern const vector<int>* (*seq_array[seq_cnt])(int);
```
好啦，你可能会说，这和“把函数声明放到头文件中，把函数定义放在程序代码文件中”类似，但是，但是，如果你的说法正确，为什么seq_cnt不需要加上extern关键词呢？
很显然，这个问题困惑你，也困惑我，困惑着我们每一个人。
让我告诉你，==const objects==就和==inline函数==一样，是“一次定义规则”下的例外，==const objects的定义只要一出文件之外便不可见==。这意谓着我们可以在多个程序代码文件中加以定义，不会导致任何错误。
（译注：读者可能会疑惑，刚才讨论过的seq_array不也是一个const object吗？不，它不是，它是一个“指向const object“的指针，它本身并不是const object。）
为什么我们会想要那么做呢？因为我们希望编译器在我们的数组声明中使用这个const object， 并且在其它需要用到常量表达式（constant expression）的场合中（那可能会跨越文件范围）也能够使用它。

> extern是c++引入的一个关键字，它可以应用于一个**全局变量，函数或模板声明**，说明该符号具有外部链接_(external linkage)_属性。也就是说，这个符号在别处定义。一般而言，C++全局变量的作用范围仅限于当前的文件，但同时C++也支持分离式编译，允许将程序分割为若干个文件被独立编译。于是就需要在文件间共享数据，这里extern就发挥了作用。

 [每日一问3： C++中extern关键字的作用](https://www.cnblogs.com/honernan/p/13431431.html)
[C/C++中extern关键字详解](https://www.cnblogs.com/yc_sunniwell/archive/2010/07/14/1777431.html)

## include头文件
为什么我们使用双引号("")而非尖括号(<>)将numseq.h括起来呢？
下面是个概略的回答：如果表头文件和包含此文件的程序代码文件位于同一个驱动器目录下，我们便使用双引号，如果在不同的驱动器目录下，我们便使用尖括号。
更具技术性的回答是，如果此文件被认定为==标准的、或项目专属的头文件==，我们便以尖括号将文件名括住：编译器搜寻此档时，会先在某些默认的驱动器目录中找寻，如果文件名由成对的双引号括住，此文件便被认为是一个用户自行提供的头文件：搜寻此文件时，会由含人此文件之文件所在的驱动器目录开始找起·

# 3 泛型编程风格
StandardTemplateLibrary(STL)主要由两种组件构成：一是容器（container），包括vector,list, set,map等类．另一种组件是用以操作这些容器类的所谓泛型算法(generical gorithm)，包括find()，sort(), replace()，merge()等等
vector,list这两种容器是所谓的==序列式容器==（sequentialcontainer〉·序列式容器会依次维护第一个元素、第二个元素灬灬直到最后一个元素我们在序列式容器身上主要进行所谓的迭代。
map和set这两种容器属于==关联式容器==（associativecontainer）·关联式容器可以让我们快速寻找容器中的元素值。
所谓map乃是一对key/value组合·用于搜寻，“用来表示我们要存储或取出的数据·例如，电话号码即可轻易以map表示，电话用户名称便是，而与电话号码产生关联．
所谓set，其中仅含有key．我们对它进行查询操作，为的是要判断某值是否存在于其中．如果我们想要建立一组索引表，用来记录出现于新闻、故事中出现的字，我们可能会希望将一些中性字眼如劢'，“·知排除掉·在放行某个字，让它进人索引表之前，我们先查询excluded-word这么一个set，如果这个字存在其中，我们便忽略它，不再计较：反之则将此字加人索引表

## Python Collections (Arrays)

There are four collection data types in the Python programming language:
-   **List** is a collection which is ordered and changeable. Allows duplicate members.
-   **[Tuple](https://www.w3schools.com/python/python_tuples.asp)** is a collection which is ordered and unchangeable. Allows duplicate members.
-   **[Set](https://www.w3schools.com/python/python_sets.asp)** is a collection which is unordered, unchangeable*, and unindexed. No duplicate members.
-   **[Dictionary](https://www.w3schools.com/python/python_dictionaries.asp)** is a collection which is ordered** and changeable. No duplicate members.

泛型算法：与变量类型、容器类型无关。

- array和vector都是以一段连续内存来储存所有元素，不同的是vector可以是空的，array则不然。
- list 含有前向指针和后向指针，并不是存在连续区域的。
- iterator是在“抽象层”将容器中的元素一一呈现，不需要考虑迭代的具体实现方式，例如list和array的不同。

## 泛型算法
总共超过60个（译注：实际近75个），以下列出一部分：
- 搜寻算法（search）：find(), adjacent_find(), find_if(), count_if(), binary_search(), find_first_of().
- 排序及调序算法(sorting, ordering)：merge(), partial_sort(), partition(), random_shuffle(), reverse(), rotate(), sort().
- 复制、删除、替换算法（copy, delete, substitution）： copy(), remove(), remove_if(), replace(), replace_if(), swap(), unique().
- 关系算法（relational）：equal(), includes(), mismatch().
- 生成与变异算法（generation, mutation）：fill(), for_each(), generate(), tranform().
- 数值算法（numeric）:accmulate(), adjacent_difference(), partial_sum(), inner_product().
- 集合算法（set）：set_union(), set_difference().
 
 [C++ Primer - 泛型算法](https://r00tk1ts.github.io/2018/11/24/C++%20Primer%20-%20%E6%B3%9B%E5%9E%8B%E7%AE%97%E6%B3%95/)

- vector是连续内存储存，适合随机读取，不适合在中部插入。`#include <vector>`
- list是double-linked，适合随机写入，不适合随机读取，因为取值需要遍历。`#include <list>`
- deque是连续内存储存，实现queue的功能。`#include <deque>`

## Function objects
所谓function objects，是某种class的实体对象，这类class对function call运算符进行了重载操作，如此一来，可使function objects被当成一般函数来使用。
function objects实现出我们原本可能以独立函数加以定义的事物，但又何必如此呢？
主要是为了效率。我们可以==令call运算符成为inline==，因而==消除“通过函数指针来调用函数”时需付出的額外代价==。
`#include <functional>`
- funtion objects adapter： bind1st(), bind2nd()，给某个funtion object绑定输入参数。

## 3.6 小结
- 开始我写了一个函数，它可以找出vector内小于10的所有元素。然而函数过于死板，没有弹性。
- 接下来我为函数加上一个数值参数让用户得以指定某个数值，以此和vector中的元素做比较
- 后来，我又加上一个新参数一个函数指针，让用户得以指定比较方式，
- 然后，我引人funtion objects的概念，使我们得以将某组行为传给函数、==此法比函数指针的做法效率更高==．我也带领各位简短地检了标准程序库提供的funcuon objects。第四章会告诉各位如何撰写自己的funcuon objects。
- 最后，我将函数以function template的方式重新实现。为了支持多种容器，我传人一对iterator，标示出一组元素范围：为了支持多种元素型别，我将元素型别参数化，也将施用于元素身上的“比较操作”参数化，以便得以同时支持==函数指针==和==funcuon object==两种方式。
- 现在，我们的函数和元素型别无关，也和比较操作无关，更和容器型别无关。简单地说，我们已经将最初的函数转化为一个泛型算法了，

## 3.7 使用map
map是一对（pair）数值（key/value），其中key通常是个字符串，作为索引，另一个是value。
```C++
#include <map>
#include <string>
map<string, int> words;
words["key"] = value;
```
查询某key是否在map中，可以用find， count等成员函数。

## 3.8 使用set
set可用来去重，把list“传入”set后，重复的值只保留一个，并且降序。

## 3.9 iterator insertor
声明容器时并不知道容量大小，为避免空间浪费，使用insert逐一添加成员。

```c++
#include <iostream>
#include <iterator>
#include <fstream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;
int main()
{
    ifstream in_file("input_file.txt");
    ofstream out_file("output_file.txt");
    if (!in_file || !out_file)
    {
        cerr << " !! unable to open the necessary files.\n";
        return -1;
    }
    istream_iterator<string> is(in_file);
    istream_iterator<string> eof;

    vector<string> text;
    copy(is, eof, back_inserter(text));

    sort(text.begin(), text.end());
    ostream_iterator<string> os(out_file, " ");
    copy(text.begin(), text.end(), os);
}
```

# 4 基于对象的编程风格
## class定义
- private members只能被member function或者class friend内被调用。
- 成员函数必须在主体内部声明，但是可以在外部实现。
 默认情况下在内部实现的函数是inline函数，在外部实现的函数需要加上inline关键字才能实现同样效果。
## member initialization list （成员初值表）
constructor定义式的第二种初始化语法，是所谓的member initialization list （成员初值表）。
```c++
Triangular::Triangular(const Triangular &rhs)
    :_length(rhs._length), _beg_pos(rhs._beg_pos),_next(rhs._beg_pos-1)
    {} //是的，空的
```
member initialization list 紧接在参数表最后的冒号后面，是个以逗号为分割的列表。就像是在调用各个member的constructor。
- 由于本例中data members中没有objects，故第一种与第二种constructor是等效的。（第一种就是用`int a = input;`的形式传值）。
- ==即使是传址，写在成员初值表里仍然比在constructor里面要高效一些。==
- 后面还有讨论：[[C++笔记#member initialization list VS inside constructor]]

- 析构函数因为返回值为空，参数列表也为空，所以不能被重载（overload）。p127。为啥？
- 非virtual函数也能被重写（override），但是不能通过基类指向子类的函数。

## inline函数
[C++ 内联函数](https://www.runoob.com/cplusplus/cpp-inline-functions.html) [inline百度百科](https://baike.baidu.com/item/inline)
- 概述：为了取代C中的define表达式，减少函数调用带来的时间消耗，是一种空间换时间的方法。
- 实现过程：编译器会将内联函数在每个被调用处展开，而非通过函数调用去实现。这样可以加快执行速度。
> 程序在编译器编译的时候，编译器将程序中出现的内联函数的调用表达式用内联函数的函数体进行替换，而对于其他的函数，都是在运行时候才被替代。这其实就是个空间代价换时间的i节省。所以内联函数一般都是1-5行的小函数。在使用内联函数时要留神：

- 类的数据成员前加一般加`“_”`，与成员函数区分？
- class中变量初值为其索引值。
- class的复制也涉及深浅拷贝问题。
- 重载的函数 一般要在相应的类中定义为friend function，与属性相比更高效。

## 4.3 mutable和const
- class的设计者应该紧跟在成员函数的参数表后面标记const，以告知编译器：这个成员函数不会改变==调用者（object）==的值。
- 这样才适合被形如`int function(const &input){}`的函数调用。
>trian是个const reference参数，因此，编译器必须保证trian在sum()中不被修改。但是sum()调用的任何一个member function都有可能更改trian的值。为确保调用者的内容不被修改，编译器必须保证被调用的成员函数不会改动调用者。这可以通过在member function后标注const来实现。

- 编译器会检查被标记为const的函数是否真的没有改变object的内容。

## 4.9 function object
是一种提供了“function call运算符”的类。
对于`lt(val);`，编译器认为`lt`可能是函数名、函数指针，也可能是提供了function call运算符的function object。
若是function object，会将其转换成`lt.operator(val); `

## 4.10 iostream重载
重载`<<`以输出更复杂的内容，比如类。

## 4.11 指针：指向class member functions
typedef existing_type new_name; ==page 142, 151==
用来为某个类型指定一个新名字，或者理解为定义了一个新类型。
```c++
typedef void (num_sequence::*PtrType)(int);
//可寻址下列函数
void func0(int);
void func1(int);
void func2(int);

PtrType pm = &num_sequence::func0;
```

maximal munch编译规则：将“符号序列”按照最长的可能序列解释。例如:
```c++
vector<vector<int>> //失败，因为>>可以被解释为iostream运算符
vector<vector<int> > //成功。加一个空格。
```

pointer to member function he pointer to function的一个不同点是，前者必须通过同类的对象加以调用，而该对象便是member function内的this指针所指之物。假设有以下定义：
```c++
num_sequence ns;
num_sequence *pns = &ns;
PtrType pm = &num_sequence::fibonacci;
```
为了经由ns调用_pmf，我们使用如下代码：
```c++
//the same with ns.fibonacci(pos)
(ns.*pm)(pos);
```
其中的`.*`符号是个“pointer to member selection运算符”，系针对class object运行，必须加上小括号才行。
若是pointer to class object运行的"pointer to member selection运算符"，则使用`->*`：
```c++
//the same with ns.fibonacci(pos)
(pns->*pm)(pos);
```

# 5 面向对象编程风格
## object-based VS object-oriented
class的主要用途是引入一个崭新的数据类型，更直接地表现目标对象。
当应用系统中布满许多类，其间有着“is-a-kind-of”的关系，基于对象（object-based）的编程模型反而会拖累程序设计工作。
因为这种模型（object-based）无法进一步指出==类间关系==.
要指出这种类间关系，需要面向对象的编程模型（object-oriented）

## 5.1 object-oriented concept
### 最重要的两个特性：继承、多态。
前者使我们得以将一群相关的类组织起来，并让我们得以分享其间的共同数据和操作，后者让我们在这些类之上进行编程，可以如同操控单一个体，而非相互独立的类，并提供更多弹性来加入或移除任何特定类。

父类被称为基类（base class），子类称为派生类（derived class）。
- 我们的程序中不存在LibMat对象，只有Book, RentalBook等派生类的对象。我们通过mat（LibMat的对象）调用check_in()时，会发生什么事？
- 每次check_in()被调用时，mat必须参考到实际的对象，该函数也会被解析（resolved）为mat所代表的实际对象拥有的那个check_in()函数。

### 动态绑定（dynamic binding）是第三个独特概念。
在非OOP的代码中，`mat.check_in();`在编译时就被编译器解析出要执行哪一个check_in()。这种在执行前就已解析出具体调用函数的方法叫“静态绑定”（static binding）。
但OOP代码中，编译器无法在编译时解析出具体要调用的函数，只能在执行时根据mat所寻址的实际对象来决定具体调用那个check_in()。
“找出实际被调用的究竟是哪一个派生类的check_in()函数”这一解析操作会延迟至执行期（run-time）。这就是所谓“动态绑定”（dynamic binding）。

### 存取层级：
- public: 一般程序皆可调用。
- private：只有成员间以及友元函数才可调用。
- protected: 介于上述两者之间，只能被派生类调用，其他都不行。
使用派生类时不需要区分“继承二来的成员”和“自身定义的成员”，两者的使用完全透明。

```c++
class AudioBook : public Book
{
public:
    AudioBook(const string &title,
              const string &author, const string &narrator)
        : Book(title, author), _narrator(narrator)
    {
        cout << "AudioBook::AudioBook{ " << _title << "," << _author
             << "," << _narrator
             << ") constructor\n";
    }

    ~AudioBook()
    {
        cout << "AudioBook::~AudioBook() destructor!\n";
    }

    virtual void print() const
    {
        cout << "AudioBook: :print() -- I am an AudioBook object!\n"
             // 注意，以下直接取用继承而来的
             // data members: _title 和_author
             << "My title is:"
             << _title << '\n'
             << "My author is: " << _author << '\n'
             << "My narrator is: " << _narrator << endl;
    }

    const string &narrator() const { return _narrator; }  

protected:
    string _narrator;
};

int main()
{
    AudioBook ab("Mason and Dixon", "Thomas Pynchon", "Edwin Leonard");
    cout << "The title is "
         << ab.title()
         << "\n"
         << "The author is " << ab.author()
         << '\n'
         << "The narrator is  " << ab.narrator() << endl;
}
```

==以上为C++的面向对象编程风格的主要内容，即使有遗漏，也是细枝末节。==

```c++
//枚举类型
enum ns_type {ns_set, ns_fibonacci, ns_pell};
```

### C++ 虚函数、纯虚函数、抽象类
https://www.runoob.com/w3cnote/cpp-virtual-functions.html
>它虚就虚在所谓"推迟联编"或者"动态联编"上，一个类函数的调用并不是在编译时刻被确定的，而是在运行时刻被确定的。由于编写代码的时候并不能确定被调用的是基类的函数还是哪个派生类的函数，所以被成为"虚"函数。

定义一个函数为虚函数，不代表函数为不被实现的函数。
定义他为==虚函数是为了允许用基类的指针来调用子类的这个函数==。
定义一个函数为纯虚函数，才代表函数没有被实现。
定义==纯虚函数是为了实现一个接口==，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数。

- 与重载相比，两者看似都实现了”调用一个同名函数实现不同功能“，区别是：
    - 重载是通过调用子类的函数；
    - 虚函数是通过调用基类的指针；
    - 虚函数就是重写吧？见[[C++笔记#重写与重载]]。

## 5.4 抽象基类/abstract base class
- 很像CSharp里面的接口interface。
**抽象类的定义：** 称带有纯虚函数的类为抽象类。
- 抽象类一般要将destructor也声明为virtual，为了delete时可以调用子类的析构函数以回收资源。
- 如果不声明为virtual，则调用基类的函数指针时会调用基类的函数而非子类的，这不对。

## 5.5 派生类/derived class
```C++
class num_sequence
{
public:
    virtual ~num_sequence(){};
    virtual int elem(int pos) const = 0;
    virtual const char *what_am_i() const = 0;
    static int max_elems()   { return _max_elems; }
    virtual ostream &print(ostream &os = cout) const = 0;
protected:
    virtual void gen_elems(int pos) const = 0;
    bool check_integrity(int pos) const;
    const static int _max_elems = 1024;
};
```

```C++
class Fibonacci : public num_sequence
{
public:
    Fibonacci(int len = 1, int beg_pos = 1) 
    : _length(len), _beg_pos(beg_pos) {}
    virtual int elem(int pos) const;
    virtual const char *what_am_i() const { return "Fibonacci"; }
    virtual ostream &print(ostream &os = cout) const;
    int length() const { return _length; }
    int beg_pos() const { return _beg_pos; }  

protected:
    virtual void gen_elems(int pos) const;
    int length;
    int _beg.pos;
    static vector<int> _elems;
};
```

```C++
// ok：通过虚拟函数机制，调用了 Fibonacci：：what_am_i()
ps->what_am_i();  

// ok：调用继承而来的 num＿sequence：：max_elems()
ps->max_elems();  

// 错误：1ength()并非 num_sequence 接口中的一员
ps->length();  

// ok：通过虚拟函数机制调用 Fibonacci destructor
delete ps;
```

谈到这里，我的看法是，当我们面临“萃取基类和派生类之间的性质，以决定哪些东西（包括接口和实际成员）属于谁”时，面向对象设计所面对的挑战，将不只是编程层面而已。一般而言，这是一个不断更迭的过程，过程之中借着程序员的经验和用户的回馈，不断演化成长。

派生类的虚拟函数==必须精确==吻合基类中的函数原型。在类本身之外对虚拟函数进行定义时，==不需指明关键词virtual==。

## 5.6 继承体系
运用其中的虚函数机制，提高代码复用率，减少相似代码。

## 5.7 基类应该多抽象？
如果设计完基类后，预期会提供给数学能力强于变成能力的人，也就是更关心业务逻辑的而非代码逻辑的用户，那么应该让基类提供的功能更多一些，以减少基类用户的工作量。

  data members 如果是个 reference，则==必须==在constructor的 ==member initialization list中==加以初始化。一旦初始化，就==再也无法==指向另一个对象。
  如果data members是个pointer，就无此限制：==可以==在==constructor内==加以初始化，==也可以==先将它初始化为null，稍后再令它指向某个有效的内存地址。
  在程序设计过程中我们便是==根据这些不同的性质==来决定要使用reference还是pointer。

```c++
class num_sequence{
public:
 virtual ~num_sequence() {}
 virtual const char *what_am_it() const = 0;
 int elem(int pos) const;
 ostream &print(ostream &os = cout) const;
 int length() const { return _length; }
 int beg_pos() const { return _beg_pos; }
 static int max_elems() { return 64; }  

protected:
 virtual void gen_elems(int pos) const = 0;
 bool check_integrity(int pos, int size) const;
 num_sequence(int len, int bp, vector<int> &re) : _length(len), _beg_pos(bp), _relems(re) {}

 int _length;
 int _beg_pos;
 vector<int> &_relems;
};
  
class Fibonacci : public num_sequence{
public:
 Fibonacci(int len 1, int beg_pos 1);
 virtual const char *what_ami() const { return "Fibonacci"; }  

protected:
 virtual void gen_elems(int pos) const;
 static vector<int> _elems;
};
```

## 5.8 初始化、析构、复制（initialization、detruction and copy）
- 派生类对象的初始化行为包含调用基类和派生类自己的constructor。
- 基类的destructor会在派生类的destructor结束之后被自列调用。我们不需在派生类中对它进行明确的调用操作。

## 5.9 在派生类中定义一个虚拟函数
当我们定义派生类时，我们必须决定，究竟要将基类中的虚拟函数改写掉，还是原封不动地加以继承。
如果我们继承了纯虚拟函数(pure virtual function),那么这个派生类也会被视为抽象类，也就无法为它定义任何对象。

“重定义虚拟函数时，返回类型必须完全吻合”
- 例外; 当基类的虚拟函数返回某个基类形式（通常是pointer和reference）时，派生类中的同名函数可以返回该基类的派生类。

当我们在派生类中，为了改写基类的某个虚拟函数，而进行声明操作时，不一定得加上关键词virtual。编译器会依据两个函数的原型声明，决定某个函数是否会改写其基类中的同名函数。

- 虚拟函数的静态决议（static resolution）
在两种情况下虚拟函数机制==不会==出现预期行为：
1. 在基类的constructor和destructor内；
2. 当我们使用的是基类的对象，而非基类对象的pointer或reference时。

- CPP的多态特性需要借助pointer或reference支持。
```C++
void print(LibMat object,
           const LibMat *pointer,
           const LibMat &reference)
{
    // 以下必定调用 LibMat::print()
    object.print();
    //以下一定会通过虚拟函数机制来进行决议
    //我们无法预知哪一份 print()会被调用
    pointer->print();
    reference.print();
}
```

>为了能够“在单一对象中展现多重类别”，多态（polymorphism）需要一层间接性。在C++中，唯有以基类的pointer和reference才能够支持面向对象编程概念。

当我们为基类声明一个实际对象时，同时也就配置除了足够==容纳该实际对象==的内存空间。如果稍后传入的却是个派生类对象，那就==没有足够的内存==放置派生类中的data members。
当我们将 AudioBook 对象传给print()时，只有 iwish 内的“基类子对象（也就是属于LibMat 的成分）”被复制到“为参数object而保留的内存”中。
其它的子对象（Book 成分和 AudioBook 成分）则被切掉（sliced off）了。
至于另两个参数 pointer 和 reference 则被初始化为 iWish 对象所在的内存地址，这就是为什么它们能够寻址到完整的AudioBook对象。
```C++
int main()
{
    AudioBook iWish("Her Pride of 10",
                    "Stanley Lippman”, “Jeremy Irons");
    print(iwish, &iWish, iWish);
    //...
}
```


### 重写与重载
- 两者是面向对象编程的==多态特性==的表现。
- ==多态即同名多义。==
- 重写，override，overwrite：相同参数列表，不同实现过程，相当于覆盖。CPP中基类的虚函数要被重写。
- 重载，overload：只有函数名相同，参数列表及实现过程都不同。多种constructor就是重载。

### 5.10 执行期的类别鉴定机制（RTTI: runtime type identification）
- 类似C#的反射
>另一种实现方法便是利用所谓的 typeid 运算符，这是所谓执行期型别识别机制（Run-Time Type Identification，RTTI）的一部分，由程序语言支持，它让我们得以查询多态化的 class pointer 或 class reference，获得其所指对象的实际型别。
```C++
#include <typeinfo>
inline const char* num_sequence:: 
    what_am_i() const
    { return typeid(*this).name();}
```
使用 typeid 运算符之前，必须先包含头文件 typeinfo。
typeid 运算符会返回一个 `type_info` 对 象，其中存储着与型别相关的种种信息.每一个多态类（polymorphic class），如Fibonacci，Pell等等，都对应一个 `type_info` 对象，该对象的 name()函数会返回一个`const char＊`，用以表示类名称。 `who＿am＿i()`函数中的这个表达式：`typeid(*this) `

### 需要对指针类型转换，才能使用成员函数
```C++
num_sequence *ps = &fib;
//...
if ( typeid(*ps) == typeid(Fibonacci))
// ok，ps 的确指向某个 Fibonacci 对象 
```
如果接下来我们这么写：`ps->gen_elems(64); `我们就可预期调用的是Fibonacci的gen_elems()。
然而，虽然我们从这个检验操作中知道ps的确指向某个Fibonacci对象，但直接在此通过ps调用Fibonacci的gen_elems()函数，却会产生编译错误：
```C++
//错误：因为ps 并非一个 Fibonacci指针。虽然我们知道
//它现在的确指向某个 Fibonacci 对象！
ps->Fibonacci::gen_elems(64);
```
是的，ps 并不“知道”它所寻址的对象实际上是什么型别，纵使我们知道，typeid 及虚拟函数机制也知道。

==为了调用Fibonacci所定义的gen_elems()，我们必须指示编译器，将ps的型别转换为 Fibonacci指针。==
==?为啥？多态机制（虚函数）在这里不起作用吗？==可能是因为RTTI机制？
`static_cast` 运算符可以担当起这项任务：
```C++
if ( typeid(*ps) == typeid(Fibonacci)) {
    Fibonacci ＊pf = static_cast<Fibonacci*>(ps);//无条件转换 
    pf->gen_elems(64);
}
```

`static_cast` 其实有潜在危险，因为编译器无法确认我们所进行的转换操作是否完全正确。这也就是为什么我要把它安排在“typeid 运算符的运算结果为真”的条件下的原因。
`dynamic_cast` 运算符就不同，它提供有条件的转换：
```C++
if ( Fibonacci *pf = dynamic_cast<Fibonacci*>(ps))//有条件转换
    pf->gen_elems(64);
```

`dynamic_cast` 也是一个 RTTI运算符，它会进行执行期检验操作，检验 ps 所指对象是否属于 Fibonacci类。
- 如果是，转换操作便会发生，于是 pf 便指向该Fibonacci对象。
- 如果不是， `dynamic_cast` 运算符返回0。
一旦 if 语句中的条件不成立，那么对`Fibonacci::gen_elems()`所进行的静态调用操作也不会发生。

# 6 以template进行编程
template开始被称作parameterized types。
## template parameter types
- ==template parameter can be==:
    - type parameter: `template<typename T>func(T input){}`
        - built-in types: int, float etc. `func<int>(input);`
        - class: `func<class>(input);`
    - non-type parameter: constant expressions
        - const: `template<int length> func()`
        - pointer: `template<void (*pf)(int)> classExample{}`

## 6.1 被参数化的类别
二叉树的实现：定义二叉树类，内含一个指针指向根节点；再定义一个节点类，内含节点值及左右两个子节点的指针。

在我的实现中，BTnode class template 必须和BinaryTree class template 合并使用。BTnode存储了节点实值 、节点实值的重复次数、左右子节点的指针，还记得两个相互合作的classes 需要建立友谊关系吗?
针对每一种 BTnode实体类，我们希望对应的BinaryTree 实体类能够成为其 friend，==以便能够访问对方的private member==。以下便是声明方法：
```C++
template <typename Type>
class BinaryTree; // 前置声明 (forward declaration)

template <typename valType>
class BTnode {
    friend class BinaryTree<valType>;
//...
};
```

## 6.2 class template的定义
模板类内部的定义与非模板类的没什么不同，但是在类外部定义函数时需要指明class scope。

```C++
template <typename eiemType>
class BinaryTree
{
public:
    BinaryTree();
    BinaryTree(const BinaryTree &);
    ~BinaryTree();

    BinaryTree &operator=(const BinaryTree &);
    bool empty() { return _root == 0; }
    void clear();
private:
    BTnode<elemType> *_root;
    // 将src所指之子树(subtree) 复制到car所指之子树
    void copy(BTnode<elemType> *tar, BTnode<elemType> *src);
};

template <typename elemType>
inline BinaryTree<elemType>::
    BinaryTree() : _root(0) {}
```

class scope后所有东西都被视为位于class定义城内。当我们写：
```C++
 BinaryTree<elemType>:: //在class定义域之外
     BinaryTree()       //在class定义域之内
```
第二次出现的BinaryTree便被视为class定义域内，所以不需要再加修饰。

## 6.3 Template类别参数(type parameters)的处理
==传值跟传址效率不一样。即使是传址，写在不同位置效率仍然不一样==
### 传值与传址（by value vs by reference）
此例以传值（by value）方式传参。但如果是class类型作为函数的参数，建议改为传址（by reference）方式传参，例如：
```C++
//这可以避免因Matrix对象的复制而造成的不必要成本。
bool find (const Matrix &val);
```
当然，传值的方式也没有错，只是会花较长的时间得到相同的结果，尤其是当程序频繁调用`find()`时。
```C++
//没错，只是缺乏效率
bool find( Matrix val);
```

实际运用中，不论内建类别或是class类别，都可能被指定为class template的实际类别。建议将所有template参数视为class来处理，即将其声明为`const reference`，从而使用传址（by reference），而非传值（by value）。

### member initialization list VS inside constructor
==同样是传址，写在member initialization list比写在constructor内部要高效，因为省了一步初始化==
针对constructor定义式中，我选择在member initialization list内为每个类别参数进行初始化操作：
```C++
//针对constructor的类别参数，该初始化方法较为普遍
template <typename valType>
inline BTnode<valType>::
    BTnode(const valType &val) // valType视为某种class
    : _val(val)
{
    _cnt = 1;
    _lchild = _rchild = 0;
}
```
而不选择在constructor函数本身内进行：
```C++
template<typename valType>
inline BTnode<valType>::BTnode( const valType &val )
{
    //不建议这样做，因为可能是class。会多一步_val初始化？
    _val = val;
    //OK, 他们的类别不会改变，肯定是内建类别
    _cnt = 1;
    _lchild = _rchild = 0;
}
```

这样一来，当用户为valType指定一个class类别时，可以保证效率最佳。例如，下面将valType指定为内建类别int：`BTnode<int> btni(42);`
则上述两种形式并无效率上的差异，但是如果我们这样写（是个class）：
```C++
BTnode<Matrix> btnm( transform_matrix );
```
效率就有高下之分了。因为constructor函数主体内对`_val`的赋值操作可分解为两个步骤：
1. 函数主体执行前，Matrix's default constructor 会先作用于`_val`身上；
2. 函数主体内会以copy assignment operator将`val`复制给`_val`。
但如果我们采用上述第一种方法，在constructor的member initialization list中将`_val`初始化，那么只需要一个步骤就可以完成工作：以copy constructor将`val`复制给`_val`。

==？如果是在constructor函数主体内使用`_val(val)`呢？？==
可能仍然会先将`_val`初始化，再将`val`复制给`_val`。
写在构造函数后面（member initialization list），就直接初始化成想要的样子。

重申一次，不论我们是以传值方式来传递valType，或是在constructor函数主体内给valType的data member设值，==都没有错==。但是会花费较多时间，而且可能被说“不谙C++编程技巧”。
如果你刚开始学习C++，其实无需过度关心效率方面的议题。然而指出这两种情形的差异却是十分有用的。不仅因为这是一般初学者常犯的错误，而且仅仅需要稍加留心，便足以走上正轨。

## 6.4 实现一个class template
- insert():
以insert()为例，如果根节点之值尚未设定，它会由程序的自由空间（free store）配置一块新的BTnode需要的内存空间，否则就调用BTnode的insert_value()，将新值插入二叉树中：
```C++
template <typename elemType>
inline void BinaryTree<elemType>::
    insert(const elemType &elem)
{
    if (!_root)
        _root = new BTnode<elemType>(elem);
    else
        _root->insert_value(elem);
}
```

==new 表达式可分为两个操作：==
1. 向程序的自由空间（free store）请求内存。
    - 如果配置到足够的空间，就返回一个指针，指向新对象。
    - 如果空间不足，会抛出`bad_alloc`异常。详见第7章。
2. 如果第一步成功，并且外界指定了一个初值，这个新对象便会以最恰当的方式被初始化，对class类型来说：
```C++
_root = new BTnode<elemType>(elem);
``` 
elem会被传入BTnode constructor。如果配置失败，初始化操作就不会发生。

```C++
template <typename valType>
void BTnode<valType>::
    insert_value(const valType &val)
{
    if (val == _val)
    {
        _cnt++;
        return;
    }
    1f(val < _val)
    {
        if (!_ichild)
            _lchild = new BTnode(val);
        else
            _lchild->insert_value(val);
    }
    else
    {
        If(!_rehild)
            _rchild = new BTnode(val);
        else _rchild->insert_value(val);
    }
}
```

- remove():
移除某值的操作更为复杂，因为我们必须保持二叉树的规则不变。一般的算法是，以节点的==右子节点取代节点本身，然后搬移左子节点==，使它成为you子节点的左子树的叶节点。如果此刻并无右子节点，那么就以左子节点取代节点本身。为了简化，我把根节点的移除操作以特例处理。
```C++
remove_value(const valType &val, BTnode *& prev);
```
remove_value()的参数列说明这两个参数都是以传址（by reference）的方式传递，这是为了避免“当valType被指定为class类别时，因传值（by value）而产生的昂贵复制成本”。由于我们并不会改变val的值，所以将它限定为const。

第二个参数就不那么直观了。为什么我们将prev以一个reference to pointer来传递呢？难道单纯地pointer还不够吗？
不，不够，==以pointer来传递，我们能够更改的是pointer所指之物==，而不是pointer本身。为了改变pointer本身，我们必须再加一层间接性。如果将prev声明为==reference to pointer==，我们不但可以==改变其所指对象==，==还可以改变pointer本身==。


## 6.5 一个以function template完成的output运算符

```C++
template <typename elemType>
inline ostream &
    operator<<(ostream &os, const BinaryTree<elemType> &bt)
{
    os << "Tree: " << endl;
    bt.print(os); // 译注：print()将于稍后说明
    return os;
}
```

print()乃是BinaryTree class template的一个private member function。为了让上述的output运算符得以顺利调用print()，output运算符必须成为BinaryTree的一个friend：
```C++
template <typename elemType>
class BinaryTree{
    friend ostream&
        operator<<(ostream &, const BinaryTree<elemType> &);
    //...
};
```

## 6.6 常量表达式与默认参数

template参数并不是非要某种类型（type）不可，也可以是常量表达式（const expressions），也被称为非类别参数（non-type parameters）。
例如，先前的数列类继承体系可以重新以class template设计，将“对象所含之元素数目”参数化：
```C++
template <int len>
class num_sequence{
public:
    num_sequence(int beg_pos = 1);
    // ...
};  

template <int len>
class Fibonacci : public num_sequence<len>{
public:
    Fibonacci(int beg_pos = 1)
        :public num_sequence<len>(beg_pos) {}
    // ...
};

Fibonacci<16> fib1();   //一个含有16个数，起始位置为1的数列
Fibonacci<16> fib2(17); //一个含有16个数，起始位置为17的数列
```

全局域（global scope）内的==函数及对象==，==其地址==也是一种常量表达式。因此他们也可以被拿来表现此一形式参数。例如，以下是一个接受函数指针作为参数的数列类：
```C++
template <void (*pf)(int pos, vector<int> &seq)>
class numeric_sequence{
public:
    numeric_sequence(int len, int beg_pos = 1)
    {
        //为了稳健起见，检查一下pf是否为null
        if (!pf)
            //产生错误并退出

        _len = len > 0 ? len : 1;
        _beg_pos = beg_pos > 0 ? beg_pos : 1;
        pf(beg_pos + len - 1, _elem);
    }
};
```

此例中的pf是一个指向“依据特定数列类别，产生pos个元素，放至vector seq内”的函数，用法如下：
```C++
void fibonacci(int pos, vector<int> &seq);
void pell(int pos, vector<int> &seq);
//...
numeric_sequence<fibonacci> ns_fib(12);
numeric_sequence<pell> ns_pell(18, 8);
```


## 6.7 以template参数作为一种设计策略
template不仅可以将内置类型作为参数，也可以将class, function pointer等作为参数。以此可衍生出多种设计。

## 6.8 member template functions
non-template class里也可以定义template function。
调用时似乎不用再指明变量类型？

# 7 异常处理/exception handling
class的设计者通知class的用户，发生了什么错误。这就是异常处理机制（exception handling facility）
## 7.1 抛出异常/throwing an exception
- 异常处理机制的两个主要部分：异常的识别与发出，异常的处理。
- 异常出现后，正常程序执行被暂停（suspended），异常处理机制寻找能处理这一异常的函数。处理完毕后，正常程序恢复（resume），从异常处理点继续执行。

C++ 通过 throw 表达式产生一个异常 :
```C++
inline void Triangular_iterator::check_integrity()
{
    if (_index >= Triangular::_max_elems)
        throw iterator_overflow(_index, Triangular::_max_elems);
    if (_index >= Triangular::_elems.size() )
        Triangular::gen_elements(_index + 1);
}
```

==throw表达式类似于一个函数调用==，他可以抛出某个对象。该对象可以是数字、字符串、或者专门的异常类。该异常类用来储存一些关于此次异常的必要信息。

```C++
class iterator_overflow
{
public:
    iterator_overflow(int index, int max)
        : _index(index), _max(max) {}  

    int index() { return _index; }
    int max() { return _max; }
    void what_happened(ostream & os = cerr)
    {
        os << "Internal error: current index "
           << _index << " exceeds maximum bound: "
           << _max;
    }

private:
    int _index;
    int _max;
};

//发生异常，抛出
if (_index > Triangular::_max_elems)
{
    iterator_overflow ex(_index, Triangular::_max_elems);
    throw ex;
}
```


## 7.2 捕捉异常/catching an exception
可以用单一或连串的catch子句来捕捉（catch）被抛出的异常对象。
catch子句由3部分构成：关键词catch，小括号内的一个类型，大括号内的用来处理异常的代码块。

```C++
// 以下代码定义于其他位置
extern void 1og_message(const char *);
extern string err_messages[];
extern ostream log_file;
bool some_function()
{
    bool status = true;
    //假设程序执行到此处
    
    throw 42;
    throw "panic: no buffer!";
    throw iterator_overflow(_index, Triangular::_max_elems);
    
    catch (int errno)
    {
        log_message(err_messages[errno]);
        status = false;
    }
    catch (const char *str)
    {
        log_message(str);
        status = false;
    }
    catch (iterator_overflow &iof)
    {
        iof.what_happened(log_file); // 该函数定义见上文
        status = false;
    }  

    // 最后一行
    return status;
}
```

是的，异常对象的类别会被拿来逐一地和每个catch子句比较。如果类别符合，那么该catch子句内容便会被执行。
举个例子，当我们抛出iterator_overflow对象时，3个catch子句会被依次检查。其中第三个子句所声明的异常类别与被抛出的异常对象相符，于是其后的语句内容便被执行起来。

==如果好几个catch都符合怎么办？会都执行吗？==
也许catch(sametype)不合法？所以问题不成立？
或者多个catch子句都执行。因为rethrow之后还会被相同类型的catch接手。

以上呈现了完整的异常处理过程。流程==通过所有的catch子句之后==，由正常程序重新接手。本例之中，正常程序会被重新接手于`return status`这一行。
有时候我们可能无法完成异常的完整处理。在记录消息之外，我们或许需要重抛（rethrow）异常，以寻求其他catch子句的协助，做进一步的处理：
```C++
catch(iterator_overfloww &iof)
{
    log_message(iof.what_happened() );
    //重抛异常，另一个catch子句接手处理
    throw;
}
```

重抛时，只需要写下关键词throw即可，他==只能出现于catch子句==中。它会捕捉到异常对象再一次抛出，并由==另一个类型吻合的catch子句接收处理==。
如果我们想要捕捉任何类型的异常，可以使用全部捕捉（catch-all）的方式。只需要在异常声明部分制定省略符号（...）即可，像这样：
```C++
catch( ... ) //catch-all，捕捉所有类别的异常
{
    log_message("exception of unknown tye");
    //clean up and exit
}
```

## 7.3 提炼异常/trying for an exception
- try-throw-catch
catch子句应该和try代码块相应而生。try块以关键词try作为开始，然后是大括号内的一连串程序语句，catch子句置于try块的尾端，这表示如果try块内有==任何异常==发生，便由接下来的catch子句加以处理。（当然也可以只处理指定的异常类型。）
```C++
bool has_elem(Triangular_iterator first,
              Triangular_iterator last, int elem)
{
    bool status = true;
    try
    {
        while (first != last)
        {
            if (*first == elem)
                return status;
            ++first;
        }
        //应该有throw
    }
    // try块内如果抛出任何异常，仅捕捉iterator_overflow
    catch (iterator_overflow &iof)
    {
        log_message(iof.what_happened());
        log_message("check if iterators address same container");
    }

    status = false;
    return status;
}
```

[another code example](http://c.biancheng.net/view/422.html)
```C++
#include <iostream>
using namespace std;
int main()
{
    double m, n;
    cin >> m >> n;
    try
    {
        cout << "before dividing." << endl;
        if (n == 0)
            throw -1; //抛出int类型异常
        else
            cout << m / n << endl;
        cout << "after dividing." << endl;
    }
    catch (double d)
    {
        cout << "catch(double) " << d << endl;
    }
    catch (int e)
    {
        cout << "catch(int) " << e << endl;
    }
    cout << "finished" << endl;
    return 0;
}
```

异常处理机制开始检查异常从何处抛出，并判断是否位于try块内？
如果是，就检查相应的catch子句，看它是否具备处理此异常的能力。
如果有，这个异常会被处理，而正常程序也就继续执行下去。

如果不是？==会上溯至调用端看是否处在try代码块内。==

当函数的try块会发生某个异常，但并没有相应的catch子句将它捕捉时，此函数便会被中断，由异常处理机制接管，并沿着“函数调用链”一路回溯，搜寻符合条件之catch子句。

如果“函数调用链”不断被解开，一直到main()仍然找不到catch子句，会怎么样？（==会中断整个程序执行==）
C++规定，每个异常都必须被处理。因此，如果在main()内还是找不到合适的处理程序，便调用标准程序库提供的teminate()——其默认行为是中断整个程序的执行。
*（此处顺序有调整，原文中顺序并非如此。）*

## 7.4 局部资源管理/local resource management
未能保证资源被回收：
```C++
extern Mutex m;
void f()
{
    // 申请资源
    int *p = new int;
    m.acquire();
    
    process(p);
    
    // 释放资源
    m.release();
    delete p;
}
```

问题出在我们无法保证，函数执行之初所配置的资源最终一定会被释放掉。
如果process()本身或其内部调用的函数抛出异常，那么process()调用操作之后的两个用以释放资源的语句便不会被执行。在异常机制运行之下，这并不是一份好的实现。
==?异常处理完毕后，释放资源的语句不会被执行吗？？==
可以用以下代码修正，但也会有问题。
```C++
void f()
{
    try    {
        // the same code
    }
    catch (...)
    {
        m.release();
        delete p;
        throw;
    }
}
```

这样虽然可以解决我们的问题，但仍然不是个令人完全满意的解答。因为用以释放资源的程序代码出现两次。而且，捕捉异常、释放资源、重抛异常，这些操作会使异常处理例程（exception handler）的搜索时间更长。
此外，程序代码本身也更复杂了，我们希望写出更具防护性、更自动化的处理方式，在C++中这通常意味定义一个专属的class。

对对象而言，初始化操作发生于constructor内，资源的申请亦应在constructor内完成。资源的释放则应该在destructor内，这样虽然无法将资源管理自动化，却可简化程序：
```C++
#include <memory>
void f()
{
    auto_ptr<int> p(new int);
    Mutexlock m1(m);
    process(p);
    //p和m1的destructor会在此被默默调用
}
```
p和ml皆为局部（local）对象。如果process()执行无误，那么相应的destructor便会在函数结束前自动施行与p和ml身上。如果process()执行过程中有任何异常被抛出，会发生什么？
在异常处理机制终结某个函数之前，C++保证，函数中的所有局部对象的destructor都会被调用。本例之中，不论是否有任何异常被抛出，p和ml的destructor保证被调用。

`auto_ptr`是标准程序库提供的class template，它会自动delete通过new表达式配置的对象，例如先前例子中的p。使用它之前，必须`#include <memory>`
auto_ptr将的reference运算符和arrow运算符予以重载，其方式就像4.6节的iterator class那样，这使得我们得以像使用一般指针一样使用auto_ptr对象。


## 7.5 标准异常/the standard exceptions

```C++
vector<string> *init_text_vector(ifstream &infile)
{
    vector<string> *ptext = 0;
    try
    {
        ptext = new vector<string>;
        // 打开 file 和 file vector
    }
    catch (bad_alloc)
    {
        cerr << "ouch. heap memory exhausted! \n";
        // ... 清理(clean up) 并离开
    }
    return ptext;
}
```
`catch(bad_alloc) //译注：bad_alloc是个class，而非object`
并未给出任何对象，因为我们只对捕捉到的异常类别感兴趣，没有打算在catch中实际操作该对象。

==我们也可以将自定义的iterator_overflow继承于exception基类下。==
需要`#include <exception>`，然后提供自己的waht()函数。
```C++
#include <exception>
class iterator_flow : public exception
{
private:
    int _index;
    int _max;

public:
    iterator_flow : exception(int index, int max)
        : _index(index), _max(max) {}
    int index() { return _index; }
    int max() { return _max; }

    // override exception::what()
    const char *what() const;
};
```

将iterator_overflow==融入标准的exception类体系的好处==是，它可以被任何“打算捕捉抽象基类exception”的程序代码所捕捉，包括先前介绍iterator_overflow时所写的程序代码。这意味着我们不需要修改原来的代码，就可以让程序认识这个class。也不需要使用全部捕捉（catch-all）的方式来捕捉所有类。下面这个catch子句：
```C++
catch( const exception &ex)
{
    cerr << ex.what() << endl;
}
```
会捕捉exception的所有派生类，当bad_alloc异常被抛出时，它会打印“bad allocation”消息。
当iterator_overflow异常被抛出时，它会打印“internal error: current index 65 exceeds maximum bound: 64”消息。

以下是iterator_overflow what()的某种实现方式，其中运用`ostringstream`对象将输出消息格式化：
```C++
#include <sstream>
#include <string>
const char *iterator_overflow::what() const
{
    ostringstream ex_msg;
    static string msg;
    // 将输出消息写到内存内的 ostringstream 对象之中
    // 将整数值转为字符串
    ex_msg << "Internal error: current index: "<< _index 
           << " exceeds maximum bound: "<< _max;
    // 提取string对象
    msg = ex_msg.str();
    
    // 提取 const char* 表达式
    return msg.c_str();
}
```

- 使用`ostringstream class`之前，必须先`#include <sstream>`。
- `ostringstream class`提供“内容内的输出操作”，输出到一个string对象。当我们需要将多个不同类别的数据格式化为字符串时，它尤其有用。
    - 他能自动将`_index, max`这类数值对象转换为==相应的字符串==，我们不必考虑储存空间、转换算法等问题。
    - `ostringstream`所提供的一个`member function str()`，会将“与`ostringstream`对象相呼应”的那个string对象返回。
- `iostream`库也对应提供了`istringstream class`。如果我们需要将非字符串数据（例如整数值或内存地址）的字符串表达式转换为其实际类别，`istringstream`可派上用场。