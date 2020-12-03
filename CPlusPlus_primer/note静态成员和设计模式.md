<h1 align="center">静态成员及静态成员的延伸</h1>

有遇到一些设计场景吗？就是类需要它的一些成员与类本身直接相关，而不是与类的各个对象保持关联。

比如：
一个银行账户类可能需要一个数据成员来表示当前的基准利率。在这种场景中，我们抽象这个银行账户类，仔细想想，是不是这个基准利率和这个类是关联的，而非与
与每个银行账户类的对象关联。从实现效率的角度来看，没有必要对每个对象都存储利率信息。最主要的是如果利率浮动，我们所希望的对象的利率都能使用新值。
## 声明静态成员
- 静态成员的类型可以是常量、引用、指针、类类型、函数等；
- 类的静态成员存在于任何对象之外，对象中不包含任何与静态数据成员有关的数据(言下之意就是编译器给对象分配的数据结构中，不包含任何与静态成员相关的数据
这样在计算对象大小时应该不包含静态成员在内的)。

>**注意：**
>静态成员函数也不与任何对象绑定在一起，它们不包含this指针。静态成员函数不能声明成const的，而且我们也不能在static函数体内使用this指针。


做一个实验:
```C++
#include <stdlib.h>
#include <iostream>

using namespace std;

class Account{
public:
    void calculate() { amount += amount * interestRate;}
    static double rate(){return interestRate;}
    static void rate(double);
private:
    std::string owner;
    double amount;
    static double interestRate;
    static double initRate();
};

int main()
{
    cout<<"输出类所占的内存大小和对象所占的内存大小:"<<endl;
    cout<<"包含静态成员的类所占的内存大小:"<<sizeof(Account)<<endl;
    Account acc01;
    cout<<"这个类的对象所占的内存大小:"<<sizeof(acc01)<<endl;
return 0;
}
```

输出结果都是40，感觉不对啊！
参考这个连接:[C++对象模型之详述C++对象的内存布局](https://blog.csdn.net/ljianhui/article/details/46408645)

## 使用静态成员
- 我们使用作用域运算符直接访问静态成员
```C++
double r;
r = Account::rate(); // 使用作用域运算符访问静态成员
```
- 虽然静态成员不属于类的某个对象，但是我们仍然可以使用类的对象、引用或者指针来访问静态成员：
```C++
double r;
Account ac1;
Account *ac2 = &ac1;
// 调用静态成员函数rate的等价形式
r = ac1.rate(); // 通过Account的对象或引用
r = ac2->rate(); // 通过指向Account对象指针
```

- 成员函数不用通过作用域运算符就能直接使用静态成员：
```C++
class Account {
public:
  void calculate() {amount += amount * interestRate;}
  static double rate(){return interestRate;}
  static void rate(double);
private:
    std::string owner;
    double amount;
    static double interestRate;
    static double initRate();
};
```

## 定义静态成员
- 我们既可以在类的内部也可以在类的外部定义静态成员函数。
>**注意：**
>当在类的外部定义静态成员时，不能重复static关键字，该关键字只出现在类内部的声明语句：
>```C++
> void Account::rate(double newRate)
> {
>      interestRate = newRate;
> }
>```

- 因为静态数据成员不属于类的任何一个对象，所以它们并不是在创建类的对象时被定义的。这意味着它们不是由类的构造函数初始化的。而且一般来说，我们不能
在类的内部初始化静态成员。相反的，必须在类的外部定义和初始化每个静态成员。和其他对象一样，一个静态数据成员只能定义一次。

- **类似于全局变量**，静态数据成员定义在任何函数之外。因此一旦它被定义，就将一直存在于程序的整个生命周期中。

## 静态成员的类初始化
通常情况下，类的静态成员不应该在类的内部初始化。
## 静态成员能用于某些场景，而普通成员不能

