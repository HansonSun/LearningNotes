# today

对于空类
c++会默认生成六个函数,分别是 默认构造函数、默认拷贝构造函数、默认析构函数、默认赋值运算符 这四个是我们通常大都知道的。但是除了这四个，还有两个，那就是取址运算符和 取址运算符 const

```c++
class Empty
{
public:
    Empty(); // 缺省构造函数
    Empty( const Empty& ); // 拷贝构造函数
    ~Empty(); // 析构函数
    Empty& operator=( const Empty& ); // 赋值运算符
    Empty* operator&(); // 取址运算符
    const Empty* operator&() const; // 取址运算符 const
};
```
在c++中为什么可以通过&来获取对象的地址,这里就是原因,因为c++会为对象重载取地址的操作符</br>
下面要说的是 类型转换函数(类型转换运算符函数),



# 一、引言

 在语句中手动指明转换的目的类型的操作，叫显式类型转换，比如 ```double a = (double)2 / 1```，在该语句中，我们手动指明了 ```2``` 要转换成 ```double``` 类型；没有指明转换的目的类型而自动进行的操作，叫隐式类型转换，比如 ```int b = 'M'```，在该语句中，字符 ```'M'``` 会自动转换成 ```int``` 类型。

在C++ 中有一种类成员函数叫类型隐式转换函数，类型隐式转换函数包括两种，一种是**转换构造函数**，一种是**隐式转换函数**，前者能将一个其他类型转换为本类类型，后者则相反，能将一个本类类型转换成其他类型。两者的实例代码如下：
```c++
#include <iostream>

class Complex {
private:
    double real_;
    double imag_;

public:
    Complex(double real, double imag)
      : real_(real)
      , imag_(imag) {}

    // 转换构造函数
    Complex(double real)
      : real_(real)
      , imag_(0) {}

    // 隐式转换函数
    operator double() { return real_; }

    friend std::ostream& operator<<(std::ostream&, const Complex&);
};

std::ostream&
operator<<(std::ostream& os, const Complex& cp) {
    if (cp.real_) {
        os << cp.real_;
    }
    if (cp.imag_) {
        if (cp.real_) {
            os << '+';
        }
        os << cp.imag_ << 'i';
    }
    return os;
}

int
main(void) {
    Complex c1(1.2, 3.5);
    Complex c2 = 4.4; // 对 4.4 调用了转换构造函数
    double d = 1.8 + c1; // 对 c1 调用了类型转换函数
    std::cout << "c1 = " << c1 << std::endl;
    std::cout << "c2 = " << c2 << std::endl;
    std::cout << "d = " << d << std::endl;
    return 0;
}

```
运行结果为：
```shell
c1 = 1.2+3.5i
c2 = 4.4
d = 3

    1
    2
    3
```
# 二、转换构造函数

转换构造函数是构造函数的一种，只有一个形参，在没有关键字 **explicit** 修饰的情况下能将其他类型转换成本类类型。

在上述代码中，只有一个形参的构造函数 ```Complex(double real)``` 就是转换构造函数，它是怎么起作用的呢？在第 41 行的 Complex c2 = 4.4; 中，编译器需要将 4.4 转换成 Complex 类型，于是以 4.4 为形参，调用 Complex(double real)，将 4.4 转换成实数部分为 4.4 复数部分为 0 的 Complex 对象，然后再调用默认拷贝构造函数为对象 c2 进行初始化。
三、类型转换函数

类型转换函数也是类成员函数的一种，用于将本类隐式转换为其他类型。

上述代码的第 19 行 operator double() 就是**类型转换函数**，可将一个 Complex 对象隐式转换为 *double* 类型的值，它是怎么起作用的呢？在第 42 行的 double d = 1.8 + c1; 中，编译器需要将 c1 转换成 double 类型，于是调用 operator double() 进行隐式转换，返回一个 double 类型的 real_ 成员变量作为转换结果。

这里的 operator 并不代表**运算符**的意思，而是作为一个前缀修饰一个函数，表明该函数是一个重载函数，重载即量身定制。

另外，读者可能觉得像 double() 这种类型转换的语法有点陌生，毕竟常见的类型转换语法都是诸如 (int)1.4的形式，但其实 (int)1.4 等价于 int(1.4)，返回结果都是 1，因此类型转换函数的格式可以统一为：

```c++
   目标类型(源类型对象) {
    return 转换结果;
}
```



又因为 double() 是一个非静态成员函数，因此 double() 有一个 this 指针参数，第 19 行的 operator double() { return real_; } 会被编译器转换成以下形式：

```c++
operator double(Complex* this) {
    return this->real_;
}
```



其中 this 为指向具体某个 Complex 对象的指针。


类型转换函数有几大特点:
1. 不能有返回值,但是函数体内部必须返回东西
2. 函数没有参数
3. 函数不能是友元函数

下面这个例子举一反三来说明

```c++
class Person{

public:
    Person(){}
    Person(int id){
        cout<<"conversion construct"<<endl;
        this->id=id;}
    Person(Person &in){
        cout<<"copy construct"<<endl;
        this->id=in.id;}
    Person& operator=(Person& in){
        cout<<"assign construct"<<endl;
        this->id=in.id;
        // return *this;
    }
    int id=0;
};
```

要理解将变量定义和初始化放在一排的话,执行的并不是赋值构造函数而是执行的拷贝构造
当我们在程序中执行



```
int main(){
	Person p_1 = 1;
}
```

这个函数会报错,报错内容是.

```error: invalid initialization of non-const reference of type ‘Person&’ from an rvalue of type ‘Person’```

这句话函数