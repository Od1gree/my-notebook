## 6.2传参

* 传递引用是原变量的别称，所以引用类型的参数与原值绑定。
* 传递指针类型变量是copy的，但是可以通过指针直接修改指向的变量。
* C++一般使用reference。

### 6.2.2使用reference来传递参数

* 在参数不希望被函数内改变的时候需要加`const`。
* 使用引用可以避免变量被拷贝。
* 使用引用作为参数可以作为额外的返回值隐含地返回内容。

### 6.2.3使用`const`修饰词

* 函数参数为`const`的时候，可以传递非`const`的参数。
* 在函数重载的时候，不能使用`const`来区分不同的函数。
* 给非const的参数传参时，不能用字符串直接传递（会被当做const类型），必须定义一个变量，再传递。

```cpp
string::size_type find_chaar(string &s, char c, string::size_type &occurs);
find_char("Hello", 'o', ctr); // compile fail

string s = "Hello";
find_char(s, 'o', ctr); // compile pass
```

* 函数多重嵌套时，要注意 `string` -> func(`const string&`) -> inner\_func(`string &`) 的情况，inner\_func会报错：也就是`const string&`不可转换为`string&`,即使原变量为`string`。
  * 需要定义一个新`string`为`const string&`的拷贝。

```cpp
//函数定义参考上面的
bool is_sentence(const string &s)
{
// if there's a single period at the end of s, then s is a sentence
  string::size_type ctr = 0;
  return find_char(s, '.', ctr) == s.size() - 1 && ctr == 1；
}

//定义一个拷贝
std::string t = std::string(s);
```

### 6.2.4传递数组

* 以下三个表达式等价

```cpp
void print(const int*);
void print(const int[]); // shows the intent that the function takes an array
void print(const int[10]); // dimension for documentation purposes (at best)
```

* 在传参的时候编译器只会检查参数是否为`const int*`，不会检查数组长度大小。
  * 即函数参数定义成`arr[10]`，传入`arr[13]`，越界了也不会报错，访问`arr[11]`和`arr[14]`均可。
  * 因此需要手动限制数组边界。

* 限定边界的三种方法
  * 在数组结尾加flag
  * 传递数组起始指针和结尾指针
  * 传递数组长度(可以使用`std::end(arr)-std::begin(arr)`来确定数组长度,但此方法不适用于`new int(n)`定义的数组)

* 多维数组

```cpp
int *matrix[10]; // array of ten pointers
void f(int matrix[][10]); // array of ten pointers
int (*matrix)[10]; // pointer to an array of ten ints
```

### 6.2.5 main函数传参

* `argc`: 参数的数量
* `*argv[]`: 指向C数组指针的数组 二维`char`数组
  * `argv[0]`存储的是程序名称，第一个参数从`argv[1]`开始

### 6.2.6 可变参数

* 可变参数函数分为两种情况：
  * 如果所有的参数类型都是一样的，可以使用 `initializer_list`这个库类型来传参(C++11)。
  * 如果存在不同类型的参数，我们可以使用“variadic template”，也就是可变参数模板。
  * 存在不同类型的参数还可以使用"ellipsis"，但是这个方式本来是用来与C接口函数使用的。因此不建议在C++中使用。

#### `initializer_list`参数

* `initializer_list`是一个类模板，里面的所有元素都是const类型的，类似于`vector`。
* 在传递参数的时候需要用大括号将参数括起来。
* 变量的生命周期与临时变量相同（就是直接在参数区域写一个字符串）
* `initializer_list`只是一个参数，一个函数可以有多个参数，拿大括号括起来就行了。

```cpp
void error_msg(initializer_list<string> il)
{
  for (auto beg = il.begin(); beg != il.end(); ++beg) {
    cout << *beg << " " ;
    cout << endl;
  }
}
```

#### Ellipsis参数

* 因为这个参数是用来和C函数做接口的，因此它所适用的数据类型仅限C和C++共同所有的类型，因此多数Class对象都不可用。
* Ellipsis参数仅在函数的最后面出现:

```cpp
void foo(params_list, ...);
void foo(...);
```

## 6.3返回值

* 不能返回局部变量的指针或引用。

#### List Initializing 返回值

* 返回值可以装在一个列表里，其初始化和`vector`的List Initializing以及上面的`std::initializer_list`相似，即 使用大括号，里面装一样类型的数据。
* 本质就是隐含地构造了一个`vector`。
* 可以返回空vecotr。

### 6.3.3返回指向数组的指针

* 使用类型化名的声明方法

```cpp
typedef int arrT[10]; //arrT 等价于 int[10]
using arrtT = int[10]; //arrtT 等价于 int[10]
arrT* func(int i); //函数返回一个指向int[10]数组的指针
```

* 直接声明的方法

```cpp
int arr[10];
int *p1[10]; //10个指针的数组
int (*p2)[10] = &arr; //指向数组的一个指针

Type (*function(parameters))[dim];
int (*func(int i))[10];
```

* 尾置声明方法(C++11)

```cpp
auto func(int i) -> int(*)[10];
```

* 使用`decltype`, `decltype(e)`使用与`e`相同类型的类型。

```cpp
int odd[] = {1,3,5,7,9};
int even[] = {0,2,4,6,8};
// returns a pointer to an array of five int elements
// decltype(odd)返回 int[],因此在此声明后添加“*”来表示指向数组的指针
decltype(odd) *arrPtr(int i){
  return (i % 2) ? &odd : &even; // returns a pointer to the array
}
```

## 6.4 函数重载

在同一域(scope)，拥有相同函数名称，不同参数的函数称为重载。

* main函数不可被重载。
* 相同参数，返回值不同的情况下会报错。
* 当多个名称不同的函数在做类似的事情，可以将他们统一命名并重载；但是如果为了重载，牺牲了函数的易读性，给人造成困扰，就不要重载。

#### const参数问题

* 最高级(top-level)指针(本身是否为const)不能用来区分重载函数，他们是等价的。
* 归纳上一条就是参数本身是否为`const`是无所谓的。

```cpp
//下面二者等价
Record lookup(Phone*);
Record lookup(Phone* const);

Record lookup(Phone);
Record lookup(const Phone);
```

* 低级(low-level)指针或者引用(指向的类型是否为const)可以用来区分重载函数。

```cpp
//下面表达式不等价，可以用来重载
Record lookup(Account&); //Account类型的引用
Record lookup(const Account&); //const Account 的reference
Record lookup(Account*); //指向Account的指针
Record lookup(const Account*); //指向const Account的指针
```

####`const_cast`和重载

在通常情况下，程序员写出一个重载函数的参数，编译器就会容易的匹配重载函数集合中最匹配的函数，但是有些情况下存在参数类型转换(conversions)的问题。在编译器匹配过程中，会有三种情况:

* 找到了最佳匹配(best match)
* 没找到任何可匹配的，即无匹配(no match)
* 有多个可以匹配的函数，并且都不是最佳匹配，即模糊调用(ambiguous call)

#### 重载和作用域

由于C++对函数名称的寻找过程中，不检查参数类型。因此即使在外部重载了多个函数，如果在局部定义了一个同名的函数，则编译器在局部搜索到该函数，并确认局部没有同名函数之后就不再搜索了。

## 6.5一些特殊用法和特性

### 6.5.1 默认参数

函数定义时可以给参数设定默认值。

* 有默认值的参数 位于 无默认值的参数 的后面。
* 一个函数可以被重新定义好几次，并且之前定义过默认值的参数在新定义中不能重新定义默认参数，即使重新定义的默认值相同也不行。

```cpp
//按照以下顺序定义函数
std::string func_overload(int a, int b, int c); //ok
std::string func_overload(int a, int b, int c=8); //ok
std::string func_overload(int a, int b=4, int c=8); //c被重新定义了，报错
std::string func_overload(int a, int b=4, int c); //ok
```

* 局部变量不能用来当做定义函数的默认值，除此之外其他的表达式，只要是能转换为参数定义类型的，都可以。
* 在调用函数之前，改变默认表达式的值，在调用时改变会生效。
* 在调用函数之前，重新定义同名的表达式，在调用时使用之前的表达式的值。

```cpp
int wd = 80;
char def = ' ';
int foo();
void func_overload(int a = foo(), int b = wd, char c = def);
func_overload(); // calls screen(foo(), 80, ' ')
int a_func(){
  def = '*'; //改变值
  int wd = 100; //重新定义变量
  func_overload(); // calls screen(foo(), 80, '*')
}
```

### 6.5.2 内联(inline)与`constexpr`函数

有时候我们会为了一个简单的计算来定义一个函数（比如比较两个对象的值），将它封装起来会提高代码可读性，但是为了这一点计算就调用函数会增大无用开销。

* 内联函数和`constexpr`函数通常放在header里面定义，为了在编译的时候正常展开代码。

#### 内联函数调用

在编译时，使用`inline`的函数的地方就会被编译器替换内容。

内联函数通常使用在不存在递归 多次被调用 并且只有一两行的函数上。

* 内联函数定义方式如下:

```cpp
inline const string & shorterString(const string &s1, const string &s2){
  return s1.size() <= s2.size() ? s1 : s2;
}
```

* 内联函数在实际使用时:

```cpp
cout << shorterString(s1,s2) << endl;

//在编译时会转换为如下代码：
cout << (s1.size() < s2.size() ? s1 : s2) << endl;
```

####`constexpr`函数 (C++11)

* 每个参数以及返回值必须是字面类型(literal type)
* 函数体中只有一个`return`
* `constexpr`函数是隐含的内联函数，在编译阶段会替换为字面类型变量。
* `constexpr`函数允许返回一个非`const`类型的变量

```cpp
constexpr int new_sz() {return 42;}
constexpr size_t scale(size_t cnt) { return new_sz() * cnt;} //ok

int arr[scale(2)]; //ok, scale(2)是constexpr

int i=2;
int a2[scale(i)]; //error, scale(i)不是constexpr,因此无法返回constexpr。
int j = scale(i); //ok, 原因在下面
```

* `constexpr`函数不一定非要返回constant expression。

### 6.5.3 用于调试的函数

`assert`和`NDEBUG`可以实现在开发(dev)状态下执行某些代码，开发完成后在发行版中禁止这部分代码运行。

#### `assert`预处理宏

预处理宏 (preprocessor variables，注意它不是编译器) 类似于内联函数，它有一个参数作为条件 `assert(expr)`，如果`expr`为`False`，则函数输出信息，并终止程序。反之则继续执行。

预处理宏通常用于检查不可能发生的情况。

* `assert`宏在`cassert`头文件中定义，我们直接用预处理函数，不需要添加任何名字空间。
* 在头文件包含`cassert`之后，变量名不能使用`assert`。

#### `NDEBUG`预处理变量

`assert`是否执行取决于`NDEBUG`是否被定义。如果被定义，则`assert`不做任何事情。

在默认情况下`NDEBUG`不被定义。

* 定义`NDEBUG`的方法如下

```cpp
#define DEBUG
```

* 大多数编译器提供了命令行定义的功能，方法如下

```cpp
$ CC -D NDEBUG main.c  # in linux
$ CC /D NDEBUG main.c  # in Windows
```

* 我们可以使用`#ifndef`的特性手动添加开发时使用的代码

```cpp
void foo(){
  ...
  #ifndef NDEBUG
  cerr << __func__ << endl; // __func__是由编译器定义的static局部变量，存储函数名称。
  #endif
  ...
}
```

* 在debug的时候有很多内置变量可供参考使用

> `__FILE__` string存储文件名
> `__LINE__` int存储当前行数
> `__TIME__` string存储编译的时间
> `__DATE__` string存储编译的日期

## 6.6 函数匹配

有些情况下，没有可以最佳匹配的函数。

#### 确定候选函数(Candidate func)和可执行函数(viable func)

候选函数：函数名与调用函数名相同的函数集合
可执行函数：候选函数的子集，是候选函数中可以被给定的参数成功调用的函数 (函数参数类型匹配或者可以转换成定义的参数类型)。

* 第一步：找到候选函数集合。
* 第二步：找到可执行函数集合
* 第三步：从可执行函数集合中找到最佳匹配。如果没有最佳匹配，那么编译器会报歧义(ambiguous)错误

### 6.6.1 参数类型匹配

* 大多数整数字默认转换为int，即使它小于`short`的范围。
* 小数默认为double，所以传递给float参数需要类型转换。
* 指针和引用都可以根据是否为const来匹配相应的函数。

## 6.7 函数指针

函数指针的类型由函数的参数和返回值决定。

```
//原始函数
bool lengthCompare(const string&, const string&);

//定义未初始化的函数指针
bool (*pf)(const string &, const string &);
```

* 当函数名作为变量时，它会自动转化为指针。取地址符号可加可不加，二者等价。

```
//等价
pf = lengthCompare;
pf = &lengthCompare;
```

* 可以使用指向函数的指针来调用函数，不需要对指针解引用。

```
//下面三个等价
bool b1 = pf("hello", "goodbye"); // calls lengthCompare
bool b2 = (*pf)("hello", "goodbye"); // equivalent call
bool b3 = lengthCompare("hello", "goodbye"); // equivalent call
```

* 函数指针所指向的函数变化的时候不需要convert。
* nullptr等价于整型0。
* 对于重载的函数，赋值时需要完全匹配返回值和参数，否则报错。

* 当函数指针作为函数参数时，编译器自动把它按照指针看待，是否加`*`等价。

```cpp
// third parameter is a function type and is automatically treated as a pointer to function
void useBigger(const string &s1, const string &s2, bool pf(const string &, const string &));

// equivalent declaration: explicitly define the parameter as a pointer to function
void useBigger(const string &s1, const string &s2, bool (*pf)(const string &, const string &));
```

* 可以使用`typedef`或者`decltype`来简化函数定义

```cpp
//函数定义
// Func and Func2 have function type
typedef bool Func(const string&, const string&);
typedef decltype(lengthCompare) Func2; // equivalent type

//函数指针定义
// FuncP and FuncP2 have pointer to function type
typedef bool(*FuncP)(const string&, const string&);
typedef decltype(lengthCompare) *FuncP2; // equivalent type
```

#### 返回函数指针

* 与返回数组相似，使用类型化名(Type alias)来定义。

```cpp
using F = int(int*, int); // F is a function type, not a pointer
using PF = int(*)(int*, int); // PF is a pointer type

//用化名将函数指针作为返回值
PF f1(int); // ok: PF is a pointer to function; f1 returns a pointer to function
F f1(int); // error: F is a function type; f1 can't return a function
F *f1(int); // ok: explicitly specify that the return type is a pointer to function
```

* 还可以用后置类型定义

```cpp
auto f1(int) -> int (*)(int*, int);
```

* 如果需要返回的函数指针指向的函数已经被定义了，可以使用decltype简化：

```cpp
string::size_type sumLength(const string&, const string&);
string::size_type largerLength(const string&, const string&);

// depending on the value of its string parameter,
// getFcn returns a pointer to sumLength or to largerLength
decltype(sumLength) *getFcn(const string &);
```

