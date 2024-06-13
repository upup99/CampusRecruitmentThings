# Item 1 Template

```cpp
namespace {
	template<template T>
	void func(ParamType param) {

	}
}
```

> case 1 T is reference / pointer

```cpp
namespace {
	template<template T>
	class TypeDisplayer;

	template<template T>
	void func(T& param) {
		TypeDisplayer<decltype(param)> type_displayer;
	}
} // namespace

int main () {
	int x = 10;
	const int cx = x;
	const int& rx = x;

	func(x);  // T -> int, ParamType int&
	func(cx); // T -> const int, ParamType const int&
	func(rx); // T -> const int, ParamType const int&

	return 0;
}
```

1. if has reference, ignore the reference 
2. regular match

> Case 2 T is a universal reference

```cpp
namespace {
	template<template T>
	void func(T&& param) {

	}
}
```

```shell
	func(27); // T -> int, ParamType int&&
```
1. if l-value, -> T&
2. if r-value, -> normal rules applies

> Case 3 T is not a reference / pointer

```cpp
namespace {
	template<template T>
	void func(T param) {

	}
}
```

1. ignore reference & const

```shell
	func(x);  // T -> int, ParamType int
	func(cx); // T -> int, ParamType int
	func(rx); // T -> int, ParamType int
	func(27); // T -> int, ParamType int
```

> Case 4 Array Decay

```
namespace {
	template<typename T>
	class TypeDisplayer;

	template<typename T>
	void func(T param) { // 不想让数组decay为指针，就加个&
		cout << sizeof(param) << endl;
	}
} // namespace

int main() {
	// char const (&)[13]
	const char hello[13] = "hello"; // const char*  Array decay
	func(hello);

	return 0;
}
```

# Item 2 auto

类型推断
```
namespace {
	template<typename T>
	class TypeDisplayer;

	template<typename T>
	void Func1(T t) {}

	template<typename T>
	void Func2(const T t) {}

	template<typename T>
	void Func3(const T& t) {}
} // namespace

int main() {
	auto x = 10; // T -> int, ParamType int

	const auto cx = 20; // T -> int, ParamType const int
	
	const auto& rx = x; // T -> int, ParamType const int&

	return 0;
}
```

特殊情况：对于{1,2,3}auto会推断出std::initializer_list<int>，而模板不会
```
namespace {
	template<typename T>
	class TypeDisplayer;

	template<typename T>
	void Func1(T t) {
		TypeDisplayer<decltype(t)> td;
	}

} // namespace

int main() {
	auto x = { 1,2,3 };
	Func1(x); // std::initializer_list<int>

	return 0;
}
```

```
auto creatInitList() {
	return { 1,2,3 }; // error: can't deduce type;
}
```

# Item 3 decltype

基本应用：推断出变量的类型
```
namespace {
	template<typename T>
	class TypeDisplayer;
} // namespace

int main() {
	int x = 10;
	const int& c = x;

	decltype(c) a = 100;

	TypeDisplayer<decltype(c)> td;

	return 0;
}
```

decltype用在返回值的注意点
```
namespace {
	template<typename T>
	class TypeDisplayer;

	// std::vector<int> vec
	// vec[0] vec[1] -> int&
	// 引用才可改变其值
	template<typename Container, typename Index>
	decltype(auto) Func(Container&& c, Index i) {
		return forward<Container>(c)[i]; // 用auto这里return的是int, but decltype(auto) return的是int&
		// forward<Container>(c)[i], 如果原来是右值，保留右值的属性，左值保留左值的属性
	}
} // namespace

int main() {
	vector<int> vec{1, 2, 3};
	int& res = Func(vec, 0);

	return 0;
}
```

# Item 5 prefer auto to explicit type declarations(auto的妙用)

1. 减少打字的字数
2. 确保变量被初始化

```
class A {
public:
	A(){}
	A(const A& a) {
		std::cout << "in copy constructor" << std::endl;
	}

	bool operator==(const A& a) const {
		return false;
	}
};

namespace std {
	template<>
	struct hash<A> {
		std::size_t operator()(const A& a) const {
			return 1;
		}
	};
} // namespace std;

namespace {
	void Func() {
		A a1;
		A a2;
		std::unordered_map<A, int> m = { {a1, 123}, {a2, 456} };

		std::cout << "being interation";
		//for (const std::pair<A, int>& p : m) { // 每次循环都会创建一个新的对象，当然const A也不会创建
		//	std::cout << p.second << std::endl;
		//}
		for (const auto& p : m) { // 使用auto并不会创建新的对象
			std::cout << p.second << std::endl;
		}
	}
} // namespace

int main() {
	
	Func();
	return 0;
}
```

# Item7 (){}的不同点

常见不同
```
namespace {
	class Widget {
	public:
		Widget() {
			cout << "Hello in default constructor" << endl;
		}

		Widget(int a) : a_(a) {
			cout << "Hello in one argument constructor" << endl;
		}

	private:
		int a_;
	};
} // namespace

int main() {
	Widget w1(1);
	Widget w2(2);

	Widget w3(); // 在C++中，()倾向于函数声明
	Widget w4{}; // 默认构造
	return 0;
}
```

注意点
```
namespace {
	class Widget {
	public:
		Widget() {
			cout << "Hello in default constructor" << endl;
		}

		Widget(int a) : a_(a) {
			cout << "Hello in one argument constructor" << endl;
		}
		
		Widget(int a, bool b) : a_(a) {
			cout << "Hello in two argument constructor" << endl;
		}

		Widget(initializer_list<int> il) {
			cout << "Hello in initializer_list constructor" << endl;
		}
	private:
		int a_;
	};
} // namespace

int main() {
	Widget w1{ 1, false }; // Hello in initializer_list constructor, 重载中容器类型为int，可转化bool为int，改成string则不会
	Widget w2( 1, false );
	Widget w3{};
	Widget w4{ {} };
	return 0;
}
```

# Item 8 使用nullptr而不是NULL

```
namespace {
	void Func(int a) {
		cout << "Hello in int a" << endl;
	}

	void Func(void* ptr) {
		cout << "Hello in void ptr" << endl;
	}
} // namespace

int main() {
	Func(NULL); // 不知道调用int a还是int*， 但在vs2022中调用int a

	return 0;
}
```

# Item 9 Prefer alias declarations to typedefs（首选别名声明而不是类型定义）

使用typedef的弊端
```
namespace {
	// 使用typedef步骤多
	template<typename T>
	using MyAllocList = std::list<T, std::allocator<T>>;

	template<typename T>
	class Widget {
	private:
		MyAllocList<T> list;
	};
} // namespace
```

省去声明类型的麻烦
```
namespace {
	template<typename T>
	using remove_const_t = typename remove_const<T>::type; // C++ 14
} // namespace

int main() {
	const int a = 10;

	// typedef
	std::remove_const<int>::type non_const_a = a;

	// alias declarations
	//std::remove_const_t<int> non_const_a = a;
	non_const_a = 11;

	cout << non_const_a << endl;

	return 0;
}
```

# Item 10 scoped enum 不错
```
namespace {

	// 1. scoped, avoid name confilt
	// 2. scoped -> prevent conversion to int

	// scoped enum，未加class时无作用域，暴露在文件下
	enum class Color : int{
		white = 1,
		yellow = 2,
		blue = 100,
	};
} // namespace

int main() {
	Color color = Color::white;

	int color_int = static_cast<int>(color);

	cout << color_int << endl;

	/*if (color < 12) {
		cout << "Hello in color" << endl;
	}*/

	return 0;
}
```


# Item 11 最好使用 delete 而不是 private

parammeter mismatch（普通函数）
```
namespace {

	bool IsLucky(int num) {
		cout << "Hello in IsLucky Int" << endl;
	}

	bool IsLucky(char ch) = delete;
} // namespace

int main() {
	IsLucky('c'); // parammeter mismatch
	IsLucky(true);

	return 0;
}
```

parammeter mismatch（template）
```
namespace {

	template<typename T>
	void Func(T a) {
		cout << "in T a" << a << endl;
	}

	template<>
	void Func(int num) = delete;
} // namespace

int main() {
	Func<int>(5);
	Func<long>(5);
	return 0;
}
```

# Item 12 override 的重要性

检查当前类成员函数是否续写父类virtual的方法，而不是重写;
返回值可以是合理的

```
namespace {
	class Base {
	public:
		virtual Base& Func() const {
			cout << "Hello in Base" << endl;
			return const_cast<Base&>(*this);
		}

		virtual ~Base() = default;
	};

	class Child : public Base {
	public:
		virtual Child& Func() const override {
			cout << "Hello in Child" << endl;
			return const_cast<Child&>(*this);
		}

		virtual ~Child() = default;
	};
} // namespace

int main() {

	std::unique_ptr<Base> base_ptr = make_unique<Child>();
	base_ptr->Func();
	return 0;
}
```

避免不必要的拷贝
```
namespace {
	class User {
	public:
		vector<int> Getvec() &{
			cout << "hello in Getvec &" << endl;
			return vec;
		}
		
		// member reference
		vector<int> Getvec() &&{
			cout << "hello in Getvec &&" << endl;
			return move(vec);
		}

	private:
		vector<int> vec = { 1,2,3 };
	};


} // namespace

int main() {

	User user;
	user.Getvec();
	vector<int> hello1 = User().Getvec();
	vector<int> hello2 = user.Getvec();

	return 0;
}
```

# Item 13 能用const就用

```
namespace {
	void Func(const vector<int> vec) {
		vec[0] = 2; // const
	}

} // namespace

int main() {

	vector<int> values = { 1,2,3 };
	auto it = find(values.cbegin(), values.cend(), 2);
	values.insert(it, 2000);

	for (int value : values) {
		cout << value << endl;
	}

	return 0;
}
```

# Item 14 noexcept的好处

可以结合move提高函数效率，确保其不发生move的异常

move 时可能发生异常

# Item 15 constexpr 灵活性

const & constexpr的不同点
```cpp
namespace {
	class Point {
	public:
		constexpr Point(double xval, double yval) noexcept : x(xval), y(yval){}

		constexpr double GetXVal() const noexcept { return x; }
		constexpr double GetYVal() const noexcept { return y; }

	private:
		double x, y;
	};

} // namespace

int main() {
	constexpr Point p1(1.2, 3.2); // 对于function就不需要

	int sz;

	const int hello1 = sz; // 这里不报错
	constexpr int hello2 = sz; // 此处会报错，由于未赋初值

	return 0;
}
```
# Item 16 const 函数最好 thread safe

```cpp
namespace {
	class RootHelper {
	public:
		std::vector<float> GetRoot() const {
			std::lock_guard<std::mutex>(m);
			if (hasCalculated) {
				return roots;
			}
			else {
				// .. do some calculation
				// roots = {1.2, 3.4};

				hasCalculated = true;
				return roots;
			}
		}

	private:
		mutable std::mutex m;
		mutable std::vector<float> roots{};
		mutable bool hasCalculated{false};
	};

} // namespace
```

# Item 17 Rule of five

```cpp
// 定义了其中一个，那么这五个都要定义
// 可以default
namespace {
	class Widget {
	public:
		Widget() {
			cout << "In Widget()" << endl;
		}
		Widget(const Widget& rhs) {
			cout << "In Widget(const Widget& rhs)" << endl;
		}

		Widget& operator=(const Widget& rhs) {
			cout << "In Widget& (const Widget& rhs)" << endl;
			return *this;
		}

		// Move constructor
		Widget(Widget&& rhs) noexcept {

		}

		// Move operator
		Widget& operator=(Widget&& rhs) noexcept {
			cout << "In Widget& operator=(Widget&& rhs) noexcept" << endl;
			return *this;
		}

		~Widget() {
			cout << "In Destructor" << endl;
		}
	};

} // namespace

int main() {
	Widget w1{};
	Widget w2{ w1 };
	w2 = w1;
	Widget w3 = std::move(w2);
	w3 = std::move(w1);

	return 0;
}
```

# Item 18 unique_ptr

```cpp
namespace {
	class Investment {
	public:
		Investment() {
			cout << "hello in Investment constructor" << endl;
		}

		virtual ~Investment() {
			cout << "in Investment" << endl;
		}
	};

	class Stock : public Investment {
	public:
		Stock(const string& symbol, double amount) {
			symbol_ = symbol;
			amount_ = amount;
		}
		~Stock() {
			cout << "in stock destructor" << endl;
		}
	private:
		string symbol_;
		double amount_;
	};

	class RealEstate : public Investment {
	public:
		RealEstate(const string& symbol, double amount) {
			symbol_ = symbol;
			amount_ = amount;
		}
		~RealEstate() {
			cout << "in RealEstate destructor" << endl;
		}
	private:
		string symbol_;
		double amount_;
	};
	
	template<typename... RestParams>
	auto MakeInvestment(const string& type, RestParams... rest_param) {
		auto del_investment = [](Investment* investment) {
			cout << "In Custom deleter" << endl;
			delete investment;
		};

		unique_ptr<Investment, decltype(del_investment)> ptr(nullptr, del_investment);
		
		if (type == "Stock") {
			ptr.reset(new Stock(std::forward<RestParams>(rest_param)...));
		}
		else {
			ptr.reset(new RealEstate(std::forward<RestParams>(rest_param)...));
		}
		
		return ptr;
	}

} // namespace

int main() {

	auto res = MakeInvestment("Stock", "GOOG", 123);

	//unique_ptr<int> ptr(new int(100));
	//ptr.reset(new int(200));
	
	//int* a = new int(100); // destructor 不一致
	//int* aa = a;
	//int* b = new int(100);

	return 0;
}
```

# Item 19 shared_ptr

```cpp
namespace {
	class Widget {
	public:
		~Widget() {
			cout << "in Widget destrutor" << endl;
		}
	};

} // namespace

int main() {

	// use make_share
	std::shared_ptr<Widget> sp1 = std::make_shared<Widget>();

	// use shared_ptr -> shared_ptr
	std::shared_ptr<Widget> sp2 = sp1;
	cout << "how many pointers pointing at it " << sp1.use_count() << endl;
	cout << "how many pointers pointing at it " << sp2.use_count() << endl;

	// use unique_ptr
	std::shared_ptr<Widget> sp3(std::make_unique<Widget>());
	cout << "how many pointers pointing at it " << sp3.use_count() << endl;

	return 0;
}
```

# Item 20 weak_ptr

1. 看shared_ptr中的object是否被回收

```C++
auto spw = std::make_shared<Widget>();
...
std::weak_ptr<Widget> wpw(spw);
...
spw = nullptr;
if (wpw.expired()) ... // if wpw doesn't point to an object
    
std::shared_ptr<Widget> spw1 = wpw.lock(); // if wpw's expired, spw1 is null
auto spw2 = wpw.lock(); // same as above, but uses auto
```

2.  什么时候采用weak_ptr

```C++

```

# Item 21 偏向使用std::make_shared & make_unique

```C++
namespace {
class A {
    
};
// use make_unique_ptr reasons
    // 1) avoid duplicated code
    // 2) avoid resource leak
    // 3) avoid one extra memory allocation
    
// Not use make_unique reasons
    // 1) custom deleter（自定义删除）
    // 2) keep the memory for a long time（单次分配的内存需同时释放）
void Func() {
    auto res1 = std::make_unique<A>();
    auto res2 = std::unique_ptr<A>(new A()); // memory allocation
}
}
int main() {
    Func();
    return 0;
}
```

# Item 23 move & 不做move

```C++
namespace {
class A {
public:
    A() {
		std::cout << "In default constructor" << std::endl;
    }
    
    A(const A& rhs) {
        std::cout << "In copy constructor" << std::endl;
    }
    
    A(A&& rhs) {
        std::cout << "In move constructor" << std::endl;
    }
};

class Annotation {
public: 
    // move -> eligibity of being moved, convert
    // left -> r-value
    explicit Annotation(const A text) : value(std::move(text)){} // const A&& text
    // explicit Annotation(A text) : value(std::move(text)){} // A&& text
private:
    A value_;
};
}

int main() {
	A a;
    Annotation an(a);
    return 0;
}
```

std::forward is a conditional cast: it casts to an rvalue only if its argument was initialized with an rvalue

# Item 24 universal reference & 右值引用

```c++
namespace {
template<typename T>
class TypeDispalyer;
    
class A {
public:
    int a_ = 10;
}

A MakeAobject() {
    return A{10};
}

template<typename T>
// universal reference
void Func(T&& param) {
    TypeDispalyer<decltype(param)> td;
}
} // namespace

int main() {
    // 1. declare its' eligibity
    // 2. bind to temporary object
    A&& a = MakeAobject();
    a.a_ = 100;
    
    auto&& r = a;// A&
    r.a_ = 200;
    std::cout << a.a_ << std::endl;
    return 0;
}
```



# Item25 使用move对右值 forward对universal

// use std::move on rvalue references, std::forward on universal references.

```C++
namespace {
class A {
public:
    A(){}
    A(const A& rhs) {
        std::cout << "In const &" << std::endl;
    }

    A(A&& rhs) {
        std::cout << "In &&" << std::endl;
    }
};

class Widget {
public:
    // void SetName(const std::string& new_name) {
    //     std::cout << "In const &" << std::endl;
    // }

    // void SetName(std::string&& new_name) {
    //     std::cout << "In &&" << std::endl;
    // }

    template<typename T>
    void SetName(T&& new_name) {
        auto res = std::forward<T>(new_name);
    }
};
} // namespace

int main() {
    Widget w;
    // A a;
    w.SetName(A{});
    return 0;
}
```























