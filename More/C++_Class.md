## 类

类的基本思想是数据抽象和封装，数据抽象是一种依赖于接口和实现分离的编程技术。类的接口包括用户所能执行的操作，类的实现则包括类的数据成员、负责接口实现的函数体以及定义类所需的各种私有函数。

## 构造函数，析构函数

异常抛出问题：

1. 不建议在构造函数中抛出异常；
2. 构造函数抛出异常时，析构函数将不会被执行，需要手动的去释放内存
3. 析构函数不应该抛出异常；
4. 当析构函数中会有一些可能发生异常时，那么就必须要把这种可能发生的异常完全封装在析构函数内部，决不能让它抛出函数之外；
5. 从析构函数抛出异常，C++运行时系统会处于无法决断的境遇，因此C++语言担保，当处于这一点时，会调用 terminate()来杀死进程。

## 友元

友元关系是单向的，不是对称，不能传递。
关于传递性，有人比喻：父亲的朋友不一定是儿子的朋友。
那关于对称性，是不是：他把她当朋友，她却不把他当朋友？

［[友元特征](http://www.nowcoder.com/questionTerminal/f1491d455d28443e9c1a0c01ddb9d6ab)］

## 派生类构造函数：

派生类构造函数调用顺序如下

1. 基类构造函数。如果有多个基类，则构造函数的调用顺序是基类在类派生表中出现的顺序。
2. 若派生类中包含对象成员，还要进行对象成员初始化。如果有多个成员类对象则构造函数的调用顺序是对象在类中被声明的顺序。
3. 派生类构造函数。
    
析构函数正好和构造函数相反。 ([constructor_derived_class.cpp](C++_Code/constructor_derived_class.cpp))

［[构造函数中调用虚函数](http://www.nowcoder.com/questionTerminal/adb540e6222b401eb294b093b9fc6f0e)］

参考  
[构造函数：C++](https://msdn.microsoft.com/zh-cn/library/s16xw1a8.aspx)

## 构造函数初始值列表

定义变量时习惯对其初始化，而非先定义、再赋值。

    string foo = "Hello";   // 定义并初始化
    string bar;             // 默认初始化为空 string 对象
    bar = "Hello";          // 为 bar 赋一个新值

就对象的数据成员来说，如果没有在构造函数的初始化列表中显式地初始化成员，则该成员在构造函数之前执行默认初始化，在构造函数中进行的是赋值操作。但是如果成员是 const 或者引用的时候，或者成员属于某种类类型且该类没有定义默认构造函数时，`必须`进行初始化。

    class ConstRef{
    public:
        // 正确, 使用构造函数初始化列表显式地初始化引用和 const 成员.
        ConstRef(int num):const_i(num), ref_j(num){}
        /*
        ConstRef(int num){
            const_i = num;  // 错误,不能给 const 赋值
            ref_j = num;    // 错误, ref_j 没有初始化
        }
        */
    private:
        const int const_a = 0;
        const int const_i;
        int &ref_j;
    };

构造函数初始值列表只说明用于初始化成员的值，而不限定初始化的具体执行顺序。成员的初始化顺序与它们`在类定义中的出现顺序一致`，第一个成员先被初始化，然后第二个，以此类推。如果一个成员是用另一个成员来初始化的，那么两个成员的初始化顺序就很关键了！（可能的话，**尽量避免使用某些成员初始化其他成员**）。

［[初始化顺序](http://www.nowcoder.com/questionTerminal/fb01e2436c6d453abbbf9801f794165b)］  
［[类定义static与const](http://www.nowcoder.com/questionTerminal/5ab084ff358e43f392833dbcd2963872)］

## 类的静态成员

在类成员的声明之前加上关键字 static 使得成员与类本身直接相关，而不是与类的各个对象保持关联。和其他成员一样，静态成员可以是 public 或 private 的，静态数据成员的类型可以是常量、引用、指针、类类型等。类的静态成员存在于任何对象之外，对象中不包含任何与静态数据成员有关的数据。

类的静态数据成员不属于类的任何一个对象，所以它们不是在创建类的对象时被定义的，也就是说不是由类的构造函数初始化的（不能用构造函数初始化静态数据成员）。通常情况下，类的静态成员不应该在类的内部初始化，必须在类的`外部定义和初始化`每个静态成员。一种例外情况是，为静态数据成员提供 const 整数类型的类内初始值。

类似的，静态成员函数也不与任何对象绑定在一起，不包含 this 指针。静态成员函数不能声明为 const 的，而且不能在 static 函数体内使用 this 指针。既可以在类的内部定义静态成员函数，也可以在外部定义成员函数。在类的外部定义静态成员函数时，不能重复关键字 static。

虽然类的静态成员不属于类的某个对象，但我们仍可以使用类的对象、引用或者指针来访问静态成员。

静态成员可以应用于某些普通成员不能应用的场景：

1. 静态数据成员可以是不完全类型，甚至可以是它所属的类类型。而非静态数据成员则受到限制，只能声明成它所属类的指针或引用；
2. 可以使用静态数据成员作为默认实参。普通数据成员不能作为默认实参，因为它的值本身属于对象的一部分。

# 继承

继承是类的重要特性。通过继承联系在一起的类构成一种层次关系，通常在层次关系的根部有一个基类，其他类则直接或者间接地从基类继承而来，这些继承得到的类称为派生类。基类负责定义在层次关系中所有类公同拥有的成员，而每个派生类定义各自特有的成员。

派生类可以继承定义在基类中的成员，但是派生类的成员函数不一定有权访问从基类继承而来的成员，访问权限受下面因素影响。

* 继承方式；
* 基类成员的访问权限(即public/private/protected)。

继承有三种方式，即公有(Public)继承、私有(Private)继承、保护(Protected)继承。（私有成员不能被继承）

* 公有继承就是将基类的公有成员变为自己的公有成员，基类的保护成员变为自己的保护成员。
* 保护继承是将基类的公有成员和保护成员变成自己的保护成员。
* 私有继承是将基类的公有成员和保护成员变成自己的私有成员。

三种继承方式的比较

![][1]

具体代码示例参见 [C++_Inheritance.cpp](C++_Code/C++_Inheritance.cpp)

一个派生类对象包含有多个组成部分：一个含有派生类自己定义的（非静态）成员的子对象，以及一个与该派生类继承的基类对应的子对象，如果有多个基类，那么对应的子对象有多个（C++ 没有明确规定派生类的对象在内存中怎样分布）。**继承中的父类的私有变量是在子类中存在的，不能访问是编译器的行为，可以通过指针操作内存来访问的。** 具体代码示例参见 [C++_Inheritance_2.cpp](C++_Code/C++_Inheritance_2.cpp)

因为在派生类对象中含有与其基类对应的组成部分，所以可以把派生类的对象当成基类对象来使用，而且也可以将基类的指针或引用绑定到派生类对象中基类部分上。这种转换通常称为 `派生类到基类的（derived-to-base）` 类型转换。**派生类向基类的转换是否可访问由使用该转换的代码决定，同时派生类的派生访问说明符也会有影响。**（C++ Primer 访问控制与继承）

如果基类定义了一个`静态成员`，则在整个继承体系中只存在该成员的唯一定义。不论从基类派生出来多少个派生类，对于每个静态成员来说都只存在唯一的实例。

［[派生类存储，隐式转换](http://www.nowcoder.com/questionTerminal/c85f9e15e6a4410a930581ae12b9a341)］  
［[子类继承父类所有对象](http://www.nowcoder.com/questionTerminal/ff91c410e28745e8ae01537d8a888283)］  

# 多态

C++ 中，基类必须将它的两种成员函数区分开来：一种是基类希望其派生类进行覆盖的函数，另一种是基类希望派生类直接继承而不要改变的函数。对于前者，基类通常将其定义为`虚函数（virtual）`。当我们使用指针或引用调用虚函数时，该引用将被动态绑定。根据引用或指针所绑定的对象不同，该调用可能执行基类的版本，也可执行某个派生类的版本。（成员函数如果没有被声明为虚函数，则其解析过程发生在编译时而非运行时）

基类通过在其成员函数的声明语句之前加上 virtual 关键字使得该函数执行动态绑定，**任何构造函数之外的非静态函数都可以是虚函数**。如果基类把一个函数声明为虚函数，则该函数在派生类中隐式地也是虚函数（**派生类可以不重写虚函数，必须重写纯虚函数**）。［C++ primer P528］

C++中的虚函数的作用主要是实现多态机制。关于多态，简而言之就是用父类型的指针指向其子类的实例，然后通过父类的指针调用子类的成员函数。这种技术可以让父类的指针有“多种形态”，这是一种泛型技术。

虚函数（Virtual Function）是通过一张虚函数表（Virtual Table）来实现的。每个包含有虚函数的类有一个虚表，在有虚函数的类的实例中保存了虚表的指针，所以，当我们用父类的指针来操作一个子类的时候，这张虚函数表像一个地图一样，指明了实际所应该调用的函数。

C++的编译器保证虚函数表的指针存在于对象实例中最前面的位置（这是为了保证取到虚函数表的有最高的性能）。

此外：内联函数也不能是虚函数，因为其在编译时展开。虚函数是动态绑定，运行期决定的，所以内联函数不能是虚函数。

［[虚函数地址分配](http://www.nowcoder.com/questionTerminal/d50dbed9a0f44e8092f86927cb7c259f)］

`函数隐藏`是指派生类的函数屏蔽了与其同名的基类函数，规则如下：

1. 如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无 virtual 关键字，基类的函数将被隐藏（注意别与重载混淆）。
2. 如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有 virtual 关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）

参考  
[C++ 虚函数表解析](http://blog.csdn.net/haoel/article/details/1948051)




[1]: http://7xrlu9.com1.z0.glb.clouddn.com/C++_Class_1.png

