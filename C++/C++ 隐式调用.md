# C++ 隐式调用
C++这门语言晦涩难懂，有一部分原因是随处不在的隐式调用，代码明明调用这个函数，而编译器却编译出了这个函数的调用，面对这种情况，本文做一些简单的记录，帮助理解 C++ 的编译和运行逻辑，一些情况可以举一反三。

## 构造函数
派生类构造函数会首先隐式调用基类构造函数，如果派生链中有一个被调用的构造函数式 private 权限的，那么构造就会失败。析构函数也同理（注意，如果是虚析构函数，也会从派生类向基类逐个调用析构函数）。

## 析构函数
析构函数会在对象销毁时被调用，显示的方式是使用 **delete** 语句调用，如果是从自动变量，编译器会选择销毁时机，自动调用析构函数，因此以下代码编译失败。

```cpp
class A
{
protect:
	~A(){}
};

int main()
{
	A *a = new A(); # 可以通过，因为只调用了构造函数
	A a;		       # 编译失败，虽然看着只使用了构造函数，但是因为这是自动变量，编译器判断在之后自动析构的时候会调用保护构造函数，权限不够，编译失败。
	delete a;       # 编译失败，调用了保护析构函数
}

```

析构函数或从当前类向基类逐个调用析构函数析构；虚析构函数从对象的真实类开始调用析构函数，沿着继承链向基类方向逐个调用析构函数。

```cpp
class A
{
public:
	virtual ~A() { cout<<"A"<<endl; }
};

class B
{
public:
	virtual ~B() { cout<<"B"<<endl; }
};

class C
{
public:
	virtual ~C() { cout<<"C"<<endl; }
};

A *c = new C();
delete c;
//   C 
//   B
//   A

B *b = new C();
delete b;
//   C
//   B
//   A
```

>注意：virtual 有继承特性，即使子类函数不写 virtual，也会随着父类变成 virtual；

纯虚析构函数也需要给出定义，因为根据上面所说的析构函数调用链，基类的析构函数也会被调用，因此必须要给出定义。


## 类型转换函数
```cpp
class A {
	operator string();
}
bool operator==(const char*, const string&);
class string {
	bool operator==(const char*);
}

A a;
a == "ab" // compile failed!
"ab" == a // compile sucess, convert 'a' to string
```

隐式转换只能转换一次，并且 C++ 会在函数中寻找合适的转换对象，而不会进入其他类寻找方法。如这里 *a* 虽然可以转换成 string 就能成功使用 *==* ，但编译器不会进入 *string* 寻找方法，也就无法找到这个转换路径，因此 *a=="ab"* 编译失败；编译器对 *"ab"==a* 能找到合适的函数，并最后成功找到转换路径。

