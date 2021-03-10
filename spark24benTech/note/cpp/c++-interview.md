# c++ 面试题
面试官向谁负责。通常应聘者会面试几轮，收集多个面试官的 feedback，交给 hire manager 或 hire committee 决定是否录用。也就是说面试官无权单独决定是否录用这个候选人。在此前提下，面试官的任务是收集足够多的有效信息（包括面试题、答案、答题过程等等），供 hire manager 或 hire committee 决断。因此，面试题要有说服力。应聘者答上了这道面试题，能说明什么，如果没答上来，又能说明什么。不符合这一要求的不是好题目。

难度与区分度。难度定位在“一个合格的 C++ 程序员应该掌握的基本知识”，是招能用 C++ 干活的人，不是招标准控或语言律师。所有人都能做出来和所有人都做不出来的题目，没有区分度，不是好题目。所有应聘者都能通过或者所有应聘者都被刷掉的面试官也不是好面试官。

公平性与可重复性。技术面试题应该有比较公认的正确答案，怎么样算答得好，不仅由面试官一个人做出判断，hire manager 和 hire committee 也能重复这一判断。一套好题目，能起到筛选多个面试者的作用，比如“眼前这个应聘者的回答与过去六个月里的应聘者相比，处于前 10 % 的位置”，这就是不错的客观标杆。

相比起大方向的理解，细节问题例如虚表是如何实现的，或者某个关键字怎么用，某个设计模式怎么实现这类问题实在意义不大。程序员不是编译器，不应该考程序员模拟编译器的水平。程序员是人，软件工程终极的目标是服务于人，应该考程序员作为人机交互中人的一端表现出的水平，即我们为什么要用C++，以及为什么C++是这样用的。

------
## 听说过c++
> 说一下你对 `static` 的理解:`static` 有什么作用，`static`变量什么时候初始化，模板里的 `static` 变量是怎么实例化的？

- static 变量，静态局部变量保存在全局数据区(静态区)，而不是保存在栈中，每次的值保持到下一次调用，直到下次赋新值。
- static 成员变量，定义必须在类定义体的外部，在类的内部只是声明，声明必须加static，定义不需要。
- static 成员函数，同样的和成员变量一样，跟类相关的，跟具体的类的对象无关，可以通过类名来调用。static成员函数里面不能访问非静态成员变量，也不能调用非静态成员函数。
- 在一个文件里使用 static 修饰的变量或函数，只能在本文件内被访问，可以使用匿名空间来替代。
- 类中的 static 变量、方法可以被继承，但如果子类重新声明了该static变量、方法，则使用新的变量、方法。
- 在模板里的 static 变量，其定义必须放在 .h 文件中，编译时不会导致变量重复定义的错误。以GCC为例，在编译时，实例化时会在符号表里添加一个“弱符号weak symbols”，这个弱符号链接时不会导致重复定义，linker会选择一个实例，丢弃其他的（假定他们都是一样的）。

https://stackoverflow.com/questions/1553854/template-static-variable

------
> 关键字 `const` 是什么含意？

const 名叫常量限定符，用来限定特定变量，以通知编译器该变量是不可修改的。习惯性的使用 const，可以避免在函数中对某些不应修改的变量造成可能的改动。
- 关键字const的作用是为给读你代码的人传达非常有用的信息，实际上，声明一个参数为常量是为了告诉了用户这个参数的应用目的。
- 通过给优化器一些附加的信息，使用关键字const也许能产生更紧凑的代码。
- 合理地使用关键字const可以使编译器很自然地保护那些不希望被改变的参数，防止其被无意的代码修改。简而言之，这样可以减少bug的出现。

------
> 关键字 `volatile` 有什么作用，可以和 `const` 一起使用么，volatile变量意味着线程安全（atomic）么？

如果一个基本变量被volatile修饰，编译器将不会把它保存到寄存器中，而是每一次都去访问内存中实际保存该变量的位置上。这一点就避免了没有volatile修饰的变量在多线程的读写中所产生的由于编译器优化所导致的灾难性问题。所以多线程中必须要共享的基本变量一定要加上volatile修饰符。

------
> `explicit` 关键字的作用？

用于构造函数，按默认规定，只用传一个参数的构造函数也定义了一个隐式转换。但在大部分情况中，隐式转换却容易导致错误（不是语法错误，编译器不会报错）。隐式转换总是在我们没有察觉的情况下悄悄发生，除非有心所为，隐式转换常常是我们所不希望发生的。通过将构造函数声明为explicit（显式）的方式可以抑制隐式转换

------
> `static_cast`, `dynamic_cast`, `const_cast`, `reinterpret_cast` 分别用在哪种场合？

- static_cast 用于类层次结构中，基类和子类之间指针和引用的转换；用于基本数据之间的类型转换；把void转换为目标类型的指针，是及其不安全的。
- dynamic_cast主要用于类层次间的上行转换和下行转换，还可以用于类之间的交叉转换。
- const_cast 该运算符用来修改类型的const或volatile属性。除了const 或volatile修饰之外， type_id和expression的类型是一样的。
- reinpreter_cast 它可以把一个指针转换成一个整数，也可以把一个整数转换成一个指针（先把一个指针转换成一个整数，在把该整数转换成原类型的指针，还可以得到原先的指针值）。可能导致不可移植。

------
> 在C++ 程序中调用被 C 编译器编译后的函数，为什么要加 extern “C”声明？

函数和变量被C++编译后在符号库中的名字与C语言的不同，被extern “C”修饰的变量和函数是按照C语言方式编译和连接的。由于编译后的名字不同，C++程序不能直接调用C 函数。C++提供了一个C 连接交换指定符号extern“C”来解决这个问题。

------
> `main` 函数执行以前，还会执行什么代码？main函数执行完，还能有其他函数执行么？

全局对象的构造函数会在main 函数之前执行。atexit注册的函数会在main函数之后执行。

------
> 指针引用的区别

引用就是某个目标变量的“别名”(alias)，对应用的操作与对变量直接操作效果完全相同。申明一个引用的时候，切记要对其进行初始化。引用声明完毕后，相当于目标变量名有两个名称，即该目标原名称和引用名，不能再把该引用名作为其他变量名的别名。声明一个引用，不是新定义了一个变量，它只表示该引用名是目标变量名的一个别名，它本身不是一种数据类型，因此引用本身不占存储单元，系统也不给引用分配存储单元。不能建立数组的引用。

------
> 重载（overload)和重写(override，有的书也叫做“覆盖”）的区别？

重载：是指允许存在多个同名函数，而这些函数的参数表不同（或许参数个数不同，或许参数类型不同，或许两者都不同）。重写：是指子类重新定义父类虚函数的方法。
重载：编译器根据函数不同的参数表，对同名函数的名称做修饰，然后这些同名函数就成了不同的函数（至少对于编译器来说是这样的）。如，有两个同名函数：function func(p:integer):integer;和function func(p:string):integer;。那么编译器做过修饰后的函数名称可能是这样的：int_func、str_func。对于这两个函数的调用，在编译器间就已经确定了，是静态的。也就是说，它们的地址在编译期就绑定了（早绑定），因此，重载和多态无关。
重写：和多态真正相关。当子类重新定义了父类的虚函数后，父类指针根据赋给它的不同的子类指针，动态的调用属于子类的该函数，这样的函数调用在编译期间是无法确定的（调用的子类的虚函数的地址无法给出）。因此，这样的函数地址是在运行期绑定的（晚绑定）。

------
> 有哪几种情况只能用intialization list 而不能用assignment?

当类中含有const、reference 成员变量；基类的构造函数都需要初始化表。

------
> 聊一下#define的特点

说了一下预处理进行替换及define的优缺点，当多处使用同一个值使用define进行一次替换就行，函数也可以做到一次替换，为什么用define不用函数

------
> C++中为什么用模板类。

- 可用来创建动态增长和减小的数据结构
- 它是类型无关的，因此具有很高的可复用性。
- 它在编译时而不是运行时检查数据类型，保证了类型安全
- 它是平台无关的，可移植性
- 可用于基本数据类型

------
> 说下你对内存的了解？描述内存分配方式以及它们的区别？

C++编程中的内存基本构造 在C++中内存分成5个区，分别是堆、栈、全局/静态存储区、常量存储区和代码区；
- 栈，就是那些由编译器在需要的时候分配，在不需要的时候自动清楚的变量的存储区，里面的变量通常是局部变量、函数参数等。
- 堆，就是那些由new分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个new就要对应一个delete。如 果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。
- 全局/静态存储区，全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量又分为初始化的和未初始化的，在 C++里面没有这个区分了，他们共同占用同一块内存区。
- 常量存储区，这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改（当然，你要通过非正当手段也可以修改）。
- 代码区 （.text段），存放代码（如函数），不允许修改（类似常量存储区），但可以执行（不同于常量存储区）。

------
> 构造函数可否是虚函数，为什么？析构函数呢，可否是纯虚的呢？

构造函数不能为虚函数，要构造一个对象，必须清楚地知道要构造什么，否则无法构造一个对象。析构函数可以为纯虚函数。

------
> 什么是内存泄漏？面对内存泄漏和指针越界，你有哪些方法？你通常采用哪些方法来避免和减少这类错误？
> 什么是虚函数，什么是虚继承，虚函数的实现机制
> 虚函数和虚继承对于一个类求sizeof的影响有什么差别

------
> 基类的有1个虚函数，子类还需要申明为virtual吗？为什么。

不申明没有关系的。 不过，我总是喜欢显式申明，使得代码更加清晰。

------
> 如何解决多继承造成的类成员重复的问题？

使用虚继承

------
> C++里的多态是怎么实现的？动态绑定是如何实现的？

静态多态和动态多态。静态多态是指通过模板技术或者函数重载技术实现的多态，其在编译器确定行为。动态多态是指通过虚函数技术实现在运行期动态绑定的技术。

#### STL
> vector在resize时是原来的多少倍？
> `new` 和 `malloc` 有什么区别
> STL里的内存池实现
> STL里set和map是基于什么实现的。红黑树的特点？
> std::sort() 的平均复杂度、最坏复杂度（答 O(N^2) 和 O(N log N) 都算对）

------
## 会c++

> 为什么C calling convention的参数进栈顺序是从右向左的。
> class作为map/unordered_map的key分别需要实现什么？
> 使用过可变长模板吗？使用在什么场合？
> 自动化对象生命期管理，智能指针，循环引用，weak_ptr。

------
> 实现一个不能被继承的类

```cpp
template<class T>
class A {
  friend T; // 注意这里不需要class关键字
  // 将构造函数和析构函数都声明是私有的
  private :
    A() {}
    ~A() {}
};
// 这里一定需要使用虚继承，只有使用虚继承，它的子类才会直接调用A类的
// 构造函数，这样才会报错，如果是普通继承，那么通过B类调用A类的构造
// 函数时不会报错的
// 注意：在虚继承中，虚基类是由最终的派生类初始化的，换句话说，最终
// 派生类的构造函数必须要调用虚基类的构造函数。对最终的派生类来说，
// 虚基类是间接基类，而不是直接基类。这跟普通继承不同，在普通继承中，
// 派生类构造函数中只能调用直接基类的构造函数，不能调用间接基类的。
class B: public virtual A<B> {
};
class C: public B {
};
```

------
> 怎么实现一个class，禁止分配在栈上

参考singleton的实现

------
> 如何实现shared_ptr

------
> 如何实现 bind？注意点
返回函数对象，使用变参模板

------
> C++是不是类型安全的？

不是。两个不同类型的指针之间可以强制转换（用reinterpret cast)。C#是类型安全的。

------
> new 实际上执行了什么操作，可能在什么步骤出现异常(operator new 和 new 操作符有什么区别？ placement new？)
```
// new是new操作符：和sizeof()一样，不能改变
// 1.调用operator new申请空间， 2.调用构造函数
string *ps = new string("Memory");
// placement new
new(buffer) Widget(widgetSize)
```

------
> 怎么突破 private 的限制访问变量
> 虚继承的细节
> c++迭代器失效问题，一个list，将指定值删除
> list 的 insert()/erase() 与 vector 相比哪个快。（这个不是那么简单。）

------
> 标准库的线程安全性

## 比较了解c++
> 为什么C++的member function template不能是virtual的

问题的意思是，为什么在C++里面，一个类的成员函数不能既是 template 又是 virtual 的。比如，下面的代码是不合法的：
```cpp
class Animal {
  public:
      template<typename T>
      virtual void make_sound(){
        //...
      }
};
```

原因如下：因为C++的编译与链接模型是"分离"的(至少是部分原因吧)。
从Unix/C开始，一个C/C++程序就可以被分开编译，然后用一个linker链接起来。这种模型有一个问题，就是各个编译单元可能对另一个编译单元一无所知。
一个 function template最后到底会被 instantiate 为多少个函数，要等整个程序(所有的编译单元)全部被编译完成才知道。
同时，virtual function的实现大多利用了一个"虚函数表"的东西，这种实现中，一个类的内存布局(或者说虚函数表的内存布局)需要在这个类编译完成的时候就被完全确定。
所以，由上面的矛盾可知，C++ 的 member function 不能既是 template 又是 virtual 的。

------
> C++为什么要有class？

考对oop基础的理解，而不是考死语法。可引申出动态多态，RAII，类型系统，隐式成员等一票问题。大牛还是菜鸡，用这一个问题就暴露了。
类是C++用来实现OOP封装、继承和多态的核心机制。C++用虚函数实现多态，用RAII（和析构，异常机制）实现自动资源管理，用拷贝和移动定义资源的复制和转移，进而用隐式成员（Rule of 5，析构，拷贝构造，拷贝赋值，移动构造，移动赋值）来帮助用户省去手写冗余代码，最终达到不多写一个字的资源管理。如果说面向对象的概念已经有些过时了，资源管理却是永不过时的，也是C++从机制上不同于C的最主要一点。有些人写的糟糕C++代码其实是把写面向过程套了一层class的皮、滥用多态让代码纠缠不清、最终既不仅没有简化逻辑，也没有简化资源管理。

------
> 初始化列表的异常怎么捕获
```cpp
Foo::Foo(int num)
try: items_(new int[num]) {
  cout << "Foo Constructing " << num << endl;
} catch (bad_alloc& err) {
  cout << err.what() << endl;
  cout << "Foo Failed" << endl;
};
```
------
> 什么是完美转发？

模板函数形参形如 `T&&`，结合库函数 `std::forward` 来将参数实现类型的完美转发。`std::move`可将左值强制转为右值。左值可以取地址，一般在等号左边，右值不可取地址，一般在等号右边，如返回的临时变量，字面值都是右值。

------
怎么在编译器判断一个类中有没有定义某个特定的方法
主要是一些边角的语法或者是不常见的问题
逗号表达式，位域
dynamic_cast是怎么实现的？这是难题。能有思路就说明有读过ABI了。我自己不止一次被人问到过，第一次没答上来。
对于常见的主流编译器，写不写inline有什么影响
构造函数中调析构函数会有什么结果
用template 写factorial。
C++ 03有什么你不喜欢的语法或者功能？

------
> c++实现一个复数类

```cpp
#include <iostream>

class Rational {
public:
    Rational();
    Rational(int x);
    Rational(int x, int y);
    Rational(const Rational& r);
    const Rational& operator=(int x);
    const Rational& operator=(const Rational& r);
    virtual ~Rational();

    bool operator== (const Rational& other) const;

    friend const Rational operator+ (const Rational& r1, const Rational& r2);
    friend const Rational operator- (const Rational& r1, const Rational& r2);
    friend const Rational operator* (const Rational& r1, const Rational& r2);
    friend const Rational operator/ (const Rational& r1, const Rational& r2);
    friend std::ostream& operator<< (std::ostream&, const Rational& r);
    friend std::istream& operator>(std::istream&, Rational& r);

private:
    static int gcd(int p, int q);
    int num; //numerator
    int div; //divisor
};

int main(int argc, char* argv[]) {
    Rational a(1, 3); //a等于三分之一
    Rational b = 3; //b等于3，3也是有理数
    b = a;
    Rational c(a+b+Rational(1,3)); //有理数可以做加法
    std::cout << "a=" << a << std::endl;
    std::cout << "b=" << b << std::endl;
    std::cout << "c=" << c << std::endl; //输出结果

    std::cout << "Please input a Rational number like 13/15" << std::endl;
    Rational d;
    std::cin >d;
    std::cout << "value d+1=" << d+1 << std::endl;
    return 0;
}
// gcc -g -O0 -o rational Rational.cpp -lstdc++
```

------
> enable_share_from_this 是做什么的，请举一个场景说明？

让一个被shared_ptr管理生命周期的类能够在自己的成员函数内部访问shared_ptr。
然后就开始聊多线程编程模式、线程安全等问题。我觉得这个问题是一个很好的问题，从语言出发，扩展到工程经验和对编程的理解。而且弱指针确实是c++非常有特色的一个特性。

------
> 为什么stl中的内存分配器要设计为一个模板参数而不是一个构造函数参数？

这个就属于瞎聊了，各抒己见呗。最后扯到类型系统如何帮助程序员排错之类的问题。

------
> 指针是什么？你能不用指针写C++程序吗？指针好还是不好？

这个问题不仅考C基础和计算机原理基础，还可引申出引用，拷贝和移动语义，const correctness，value semantic等一票基础问题。

------
> 经典问题：vector和list有什么区别？

一个不了解C++如何控制资源颗粒度的程序员恐怕不是一个好的C++程序员。可引申出一大票算法和数据结构问题。

------
> C++为什么要有类型？

考对静态类型语言的理解和权衡，可引申出类型安全，泛型，模板元编程，编译时计算，静态多态等一众问题。

------
相比起大方向的理解，细节问题例如虚表是如何实现的，或者某个关键字怎么用，某个设计模式怎么实现这类问题实在意义不大。程序员不是编译器，不应该考程序员模拟编译器的水平。程序员是人，软件工程终极的目标是服务于人，应该考程序员作为人机交互中人的一端表现出的水平，即——我们为什么要用C++，以及为什么C++是这样用的。

------
我一般会循序渐进的问问题，以C++的虚函数为例。
了解多态么？它是怎么实现的？什么时候应该使用它？
在语言层面，虚函数是怎样实现的？为什么还要区分虚函数和普通函数，都成虚函数不好么？
虚表里存储的是什么？
如果基类定义先声明了f函数后声明了g函数，子类定义会反过来（真正面试的时候会有样例代码给出），那么虚表里第一项是什么？
如果有多继承，虚表内会怎样？
等等。
这只是个大体思路，关键是看求职者的现场反应能力和链接能力，比如：
第一题，如果提到静态多态，并且简单询问后确认懂得什么是静态多态，则有额外加分。
第二题，提到和其他语言的对比（如java oc python 等），有额外加分。
第四题，关键不在于答案，而在于回答的思路。如果在回答出大体原理之后加以说明这东西是undefined，不应该依赖于这个结果，有额外加分。
第五题，如果一开始求职者不太了解这个事情，但是通过我简单的提示，能够沿着这个思路走下去，无论对错，均有额外加分。
如果在面试中会询问到了求职者之前没有思考过的问题，我会给出适当的提示，逼迫其现场思考\

如果中间被求职者带跑了，比如问第一题的时候扯到模板元编程了，我的态度就是随着他跑，在他自己最擅长的领域才能看出他的思路究竟是怎样的。
总之，尽量通过方方面面探知求职人员对技术核心思想的把握能力，如果在面试之前他就考虑的很透彻，这是上上之选（原因是其会自己沿着正确的方向独立思考），如果在面试中可以现场考虑出大概，这是上之选（原因是其逻辑思维还不错）。
思维方式很重要，某种程度上，这东西决定一个人的命运。