<!-- GFM-TOC -->
- [其他](#其他)
- [构造函数](#构造函数)
- [析构函数](#析构函数)
- [重载赋值运算符](#重载赋值运算符)
- [继承](#继承)
- [函数模板](#函数模板)
- [运算符重载](#运算符重载)
- [命名空间](#命名空间)
- [类的强制类型转换](#类的强制类型转换)
<!-- GFM-TOC -->
---




#### 其他
* `std::endl`会flush the buffer,相反`'\n'`不会。
* * build配置
　　* release版本
　　* debug版本
* 命名冲突
  * 在同一文件当中，函数/全局变量的名称相同，将导致compiler错误。
  * 在同一项目的不同文件当中，函数/全局变量的名称相同，将导致linker错误。
* 为对象分配动态内存时应当注意：
  * 在**构造函数**中用`new`来初始化指针成员。
  * 在**析构函数**中用`delete`来释放内存，且delete和new要一一对应。
  * 定义相应的**复制构造函数**。
  * 重载相应的**赋值运算符**。
---





#### 构造函数
  * 无return
  * 声明对象举例：
    ```` cpp
    ZHP zhp(parameters...);         
    ZHP zhp = ZHP(parameters...);  
    ````
  * 构造函数的几种形式
    1. 一般形式
      ```` cpp
      ZHP::ZHP(int a, int b, int c){...}
      ````
    2. 带默认参数的形式
      ```` cpp
      ZHP::ZHP(int a, int b = 1, int c = 2){...}
      ````
    3. 带初始化列表的形式
      ```` cpp 
      ZHP::ZHP(int a, int b, int c): X(a), Y(b), Z(c) {...}
      ````
    4. 默认构造函数
      ```` cpp 
      // 1. 无输入参数。
      // 2. 用户未声明定义以上3种显示构造函数时，编译器隐式将会地为该类添加默认构造函数。
      // 3. 用户也可以自己定义一个；但是用户定义的默认构造函数与所有参数都是默认参数的构造函数存在二义性（c++pp p353）。
      ZHP::ZHP() {...}
      ````
    5. 复制构造函数
      ```` cpp 
      ZHP::ZHP(const ZHP &) {...}

      // 以下赋值方式都调用了复制构造函数
      ZHP zhp1(parameters);
      ZHP zhp2 = zhp1;        // 声明对象时直接赋值
      ZHP zhp3(zhp1);
      ZHP zhp4 = ZHP(zhp1);
      ZHP *zhp5 = new ZHP(zhp1);
      ````
---






#### 析构函数
  * 如果成员有指针变量，要考虑是否释放指针指向的内存空间。
---






#### 重载赋值运算符
  * 当利用一个对象给另一个对象赋值时，将会调用重载的赋值运算符。
  * 若用户未显示重载赋值运算符，编译器将会隐式地提供一个什么都不做的赋值运算符重载。
  * 注意与**复制构造函数**的调用情况区分。
  * 返回当前类的引用是为了可以连续赋值。
  ```` cpp 
  ZHP & ZHP::operator=(const ZHP & zhp) 
  {
    if(this == &zhp)
      return *this;
    // ...
    return *this;
  }

  // 以下赋值方式调用了重载的赋值运算符
  ZHP zhp1(parameters);
  ZHP zhp2;        
  zhp2 = zhp1;        
  ````
---






#### 继承
  * 派生类对象的基类成员将显式或隐式地调用基类的构造函数进行初始化。
  * 基类指针可以指向派生类对象；基类引用可以引用派生类对象。
    * 不过基类指针或引用只能调用基类方法。
  * 虚函数virtual
    * cpp p403
    * 纯虚函数 p416
  * protected
    * 派生类对象只能通过public方法来*间接访问*基类的**private成员**
    * 但派生类对象可以*直接访问*基类的**protected成员**
    * 在*类外*只能只能通过public方法来*间接访问*基类的**protected成员**
  * 当派生类存在动态分配的内存时，构造函数、复制构造函数、析构函数和赋值运算符的处理方法:
    * cpp p421-p423
---







#### 函数模板
1. 函数模板
  ```` cpp
  template <typename T>
  void Swap(T &a, T &b);
  ````
2. 显示具体化
  ```` cpp
  template <> void Swap<int>(int &a, int &b);
  ````
3. 显示实例化
  ```` cpp
  template void Swap<int>(int &a, int &b);
  ````
   * 实例化是函数模板的一个实例，函数体的内容必须与模板一致。而显示具体化的函数体内容可以与模板不一致。
---





#### 运算符重载
  * 类内重载
    * 重载1：运算符两边都是ZHP对象。
    ```` cpp
    zhp3 = zhp1 + zhp2;
    // 等效于：zhp3 = zhp1.operator+(zhp2);
    ZHP operator+(const zhp &b) const
    { 
      ...
    }
      ````
    * 重载2：运算符左边是ZHP对象，右边是普通类型变量。
    ```` cpp
    zhp3 = zhp1 + int1;
    // 等效于：zhp3 = zhp1.operator+(int1);
    ZHP operator+(int int1) const
    {
      ..
    }
    ````
  * 类外重载
    * 重载2：运算符左边是普通类型变量，右边是ZHP对象。
    ```` cpp
    zhp3 = int1 + zhp1;
    // 等效于：zhp3 = operator+(int1, zhp1);
    friend ZHP operator+(int int1, const ZHP &b) 
    {
      ...
    }
    ````
---



#### 命名空间
* `using namespace std`
  * std下的所有成员可以直接被使用
* `using std::cin; cin`
  * cin可以直接被使用
* `std::cin >> ...`
  * 每次使用cin时都要加上前缀`std::`
---




#### 类的强制类型转换
  1. 其它类型 -> class
    当存在如下的构造函数并且该构造函数与其它构造函数不存在二义性时: 
  ```` cpp
  ZHP::ZHP(int， doubule = 19.6) {...}
  ````
  那么以下代码是成立的，程序将使用构造函数`ZHP(int)`来创立一个临时的ZHP对象，并将100作为初始化值。随后，程序采用成员赋值方式将该临时对象的内容复制给zhp，这是一种**隐式转换**：
  ```` cpp
  ZHP zhp;
  zhp = 100;
  ````
  2. class -> 其它类型
    必须存在如下的**转换函数**: 
  ```` cpp
  ZHP::operator int()
  {
    ...
    return a_int;
  }
  ````
  将一个对象赋值给一般类型的变量才是合法的：
  ```` cpp
  ZHP zhp(19.5);
  double d = zhp;
  ````
