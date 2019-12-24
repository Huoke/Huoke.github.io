# 抽象工厂模式
## 目的
- 提供一个接口，创建相关或从属对象的族，而不指定其具体类。
- 一种层次结构，它封装了许多可能的平台，以及一套产品的构造。
- 考虑到new操作符的不利因素。
## 困扰问题
如果应用程序是可移植的，它就需要封装平台的依赖项。这些“平台”可能包括：窗口系统、操作系统、数据库等。通常这种封装事先是没有被设计好的，所以像#ifdef这样的语句和当前需要支持的所有平台的选项在代码中会频繁的出现。
## 讨论
提供一个间接层，抽象相关或依赖对象的创建，而不是直接指定它们的具体类。

“factory” 对象负责为整个平台提供一系列创建服务。客户机不直接创建平台对象，而是要求工厂为其创建平台对象。

这种机制很容易交换产品族，因为factory对象的特定类在应用程序中只出现一次———它在应用程序中被实例化。


应用程序可以通过实例化抽象工厂的不同实例，来批量替换整个产品系列。

由于factory对象提供的服务是如此普遍，因此它通常被实现为单例。

## 类结构关系
Abstract Factory(抽象工厂)为每个产品定义了个工厂方法。每个工厂方法都封装new操作符和具体的、特定于平台的产品类。每个“平台”然后用工厂派生的类建模。

![](https://sourcemaking.com/files/v2/content/patterns/Abstract_Factory.png)

## 案例分析


![](https://sourcemaking.com/files/v2/content/patterns/Abstract_Factory_example1.png)

## Check List
1. 决定“平台独立性”和创建服务是否是当前的痛苦根源。
2. 列出“平台”与“产品”的矩阵。
3. 定义由每个产品的工厂方法组成的工厂接口
4. 为每个平台定义一个工厂派生类，该类封装对新运算符的所有引用。
5. 为每个平台定义一个工厂派生类，该类封装对new运算符的所有引用。
6. 客户端应该取消对new的所有引用，并使用factory方法创建产品对象。
## Rules of thumb

## 抽象工厂用C++实现
Discussion. "Think of constructors as factories that churn out objects". Here we are allocating the constructor responsibility to a factory object, and then using inheritance and virtual member functions to provide a "virtual constructor" capability. So there are two dimensions of decoupling occurring. The client uses the factory object instead of "new" to request instances; and, the client "hard-wires" the family, or class, of that factory only once, and throughout the remainder of the application only relies on the abstract base class.


```C++
#include <iostream.h>

class Shape {
  public:
    Shape() {
      id_ = total_++;
    }
    virtual void draw() = 0;
  protected:
    int id_;
    static int total_;
};
int Shape::total_ = 0;

class Circle : public Shape {
  public:
    void draw() {
      cout << "circle " << id_ << ": draw" << endl;
    }
};
class Square : public Shape {
  public:
    void draw() {
      cout << "square " << id_ << ": draw" << endl;
    }
};
class Ellipse : public Shape {
  public:
    void draw() {
      cout << "ellipse " << id_ << ": draw" << endl;
    }
};
class Rectangle : public Shape {
  public:
    void draw() {
      cout << "rectangle " << id_ << ": draw" << endl;
    }
};

// 抽象工厂
class Factory {
  public:
    // 抽象工厂方法 创建曲线
    virtual Shape* createCurvedInstance() = 0;
    // 创建直线
    virtual Shape* createStraightInstance() = 0;
};

// 简单形状工厂
class SimpleShapeFactory : public Factory {
  public:
    Shape* createCurvedInstance() {
      return new Circle;
    }
    Shape* createStraightInstance() {
      return new Square;
    }
};

// 固定形状工厂
class RobustShapeFactory : public Factory {
  public:
    Shape* createCurvedInstance()   {
      return new Ellipse;
    }
    Shape* createStraightInstance() {
      return new Rectangle;
    }
};

int main() {
#ifdef SIMPLE
  Factory* factory = new SimpleShapeFactory;
#elif ROBUST
  Factory* factory = new RobustShapeFactory;
#endif
  Shape* shapes[3];

  shapes[0] = factory->createCurvedInstance();   // shapes[0] = new Ellipse;
  shapes[1] = factory->createStraightInstance(); // shapes[1] = new Rectangle;
  shapes[2] = factory->createCurvedInstance();   // shapes[2] = new Ellipse;

  for (int i=0; i < 3; i++) {
    shapes[i]->draw();
  }
}
```

