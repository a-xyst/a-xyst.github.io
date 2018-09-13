---
title:  "C++ 1x新特性学习"
excerpt: "Mainly learned C++ 11 & C++ 14, viewed description of C++ 17"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Language
tags:
  - C++
---

https://www.shiyanlou.com/courses/605

## 语言可用性的强化


```c++
#include <iostream>
#include <vector>
#include <initializer_list>

using namespace std;
// NULL is 0, nullptr is empty pointer

/// constexpr
const int len2 = 10;
char arr3[len2 + 5]; // wrong if no const
constexpr int fibonacci(const int n) { // constexpr for func
    return n == 1 || n == 2 ? 1 : fibonacci(n - 1) + fibonacci(n - 2);
}
int fib[fibonacci(4)];

/// auto
auto i = 5;
int arr[10] = {i};
// auto auto_arr[10] = arr; // wrong
template<typename T = int, typename U = char>
// template for param(params can have default value)
auto add(T x, U y) { return x + y; };

/// decltype
int tempA = 2;
// dclTempA is int
decltype(tempA) dclTempA;


/// initializer_list : initialize object with {}
int arr33[3] = {1, 2, 3};
struct Aa {
    int a;
    double b;
};
Aa aa{1, 2.2};
auto func(initializer_list<int> list) {
    return list;
}
auto fff = func({1, 2, 3});

/// templates
template class vector<bool>; // instantiate here
extern template class vector<double>; // do not inst in this file
vector<vector<int>> www; // no more > >
template<typename T>
using vt = vector<T>; // typedef for template
vt<int> a;
template<typename... Args> // variable param in template
void magic(Args... args) {
    cout << sizeof...(args) << endl;
}

/// unpack arguments
template<typename T, typename... Args>
auto print(T value, Args... args) {
    cout << value << endl;
    return initializer_list<T>
            {([&] { cout << args << endl; }(), value)...};
}

/// various construction
class Base { // delegate construction
public:
    int val1;
    int val2;

    Base() {
        val1 = 1;
    }

    Base(int val) : Base() {
        val2 = val;
    }

    virtual void foo(int);

    virtual void noinherit() final;
};
class Sub : public Base { // inherit construction
public:
    using Base::Base;

    virtual void foo(int) override; // explicit override
    //virtual void foo(double) override; // wrong
};
class Nodef { // explicit disable default function 
    Nodef() = default; // use default
    Nodef &operator=(const Nodef &) = delete; // no default
};

/// strong-typed enumeration
enum class NewEnum : int {
    value1 = 100
};
template<typename T> // override << to output value1
ostream &operator<<(typename enable_if<is_enum<T>::value, ostream>::type &stream, const T &e) {
    return stream << static_cast<typename underlying_type<T>::type>(e);
}

int main() {
    // simple iterate
    vector<int> arr(5, 100);
    for (auto &itr : arr) cout << itr;
    // auto
    vector<int> vec;
    for (auto itr = vec.begin(); itr != vec.end(); ++itr);
    // templates
    magic(1, "");
    // unpack arguments
    print(1, 2.1, "233");
    return 0;
}


```

## 语言运行期的强化

```c++
#include <iostream>
#include <utility>
#include <memory>
#include <functional>

using namespace std;

void lambda_1() {
    int val1 = 1;
    // copied when lambda is created
    auto copyval1 = [val1] { return val1; };
    val1 = 100;
    auto stoval1 = copyval1();
    cout << val1 << endl;
    cout << stoval1 << endl;
};


void lambda_2() {
    int val1 = 1;
    auto copyval1 = [&val1] { return val1; };
    val1 = 100;
    auto stoval1 = copyval1();
    cout << val1 << endl;
    cout << stoval1 << endl;
};


void lambda_3() {
    auto imp = make_unique<int>(1);
    auto add = [v1 = 1, v2 = move(imp)](int x, int y) -> int { // trailing return type
        return x + y + v1 + (*v2);
    }; // 1+1+3+4
    cout << add(3, 4) << endl;
};


int foo(int var) { return var; }
void sfun() { // use as function pointer
    function<int(int)> fun = foo; // return int, param is int
    int imp = 10;
    function<int(int)> fun2 = [&](int val) -> int {
        return 1 + val + imp;
    };
    cout << fun(10) << endl; // 10
    cout << fun2(11) << endl; // 1+11+10
};


int f(int a, int b, int c) { cout << a + b + c << endl; }
void sbsp() { // bind func and param
    auto bindf = bind(f, placeholders::_1, 1, 2);
    bindf(3); // 3+1+2
}


class B {
public:
    B() {}

    B(const B &) { cout << "B Constructor" << endl; }
};
class A {
public:
    A() : m_b(new B()) { cout << "A Constructor" << endl; }

    A(const A &a) : // con with lval
            m_b(new B(*(a.m_b))) {
        cout << "A Copy Constructor" << endl;
    }

    A(A &&a) : // con with rval
            m_b(a.m_b) {
        a.m_b = nullptr;
        cout << "A Move Constructor" << endl;
    }

    ~A() { delete m_b; }

private:
    B *m_b;
};
static A getA() {
    A a; // A con
    cout << "================================================" << endl;
    return move(a); // A move
}
void smove() {
    A a = getA();
    cout << "************************************************" << endl;
    A a1(a); // send lvalue: B con, A copy
    cout << "++++++++++++++++++++++++++++++++++++++++++++++++" << endl;
    A a2(move(a));// send rvalue: A move (B is not constructed, save resource)
}


void ref(int &var) {
    cout << "left" << var << endl;
}
void ref(int &&var) {
    cout << "right" << var << endl;
}
template<typename T>
void pass(T &&v) {
    cout << "normal passing" << endl; // become left
    ref(v);
    cout << "std::move passing" << endl; // become right
    ref(move(v));
    cout << "std::forward passing"<<endl; // maintain
    ref(forward<T>(v));
}
void pforward() {
    cout << "pass right" << endl;
    pass(1); // lrr
    cout << "pass left" << endl;
    int v = 1;
    pass(v); //lrl
}


int main() {
    /// lambda function
    // catch value
    lambda_1();
    // catch reference
    lambda_2();
    // implicit catch:[]void, [arg1,arg2,...]args, [&]ref, [=]val
    // catch expression
    lambda_3();
    /// function object wrapper
    // std::function
    sfun();
    // std::bind, std::placeholders
    sbsp();
    /// right value reference
    /*rvalue:dies after expression ends
    pure rvalue: 10, true, 1+2, fun return, lambda
    expiring value: expression related with rvalue reference(T &&)*/
    // std::move: change lvalue param to rvalue, can extend xvalue's life
    smove();
    // perfect forward: maintain param type
    pforward();
    return 0;
}

```

## 新增容器

```c++
#include <iostream>
#include <array>
#include <unordered_map>
#include <tuple>
#include <unordered_set>
#include <algorithm>

using namespace std;

template<typename T>
auto tuple_len(T &tpl) {
    return tuple_size<T>::value;
}

template<size_t I = 0, typename... Tp>
inline typename enable_if<I == sizeof...(Tp), void>::type
print(tuple<Tp...> &t) {}

template<size_t I = 0, typename... Tp>
inline typename enable_if<I < sizeof...(Tp), void>::type
print(tuple<Tp...> &t) {
    cout << get<I>(t) << endl;
    print<I + 1, Tp...>(t);
}

int main() {
    // thin wrapper of C-style array
    array<int, 4> arr = {1, 2, 3, 4};
    sort(arr.begin(), arr.end());
    // ordered: insert and search average O(log(size))
    // pro: sorted, less memory, can compare, better worst case
    // unordered: hashed, O(C)
    // pro: better average case
    unordered_map<int, int> map;
    unordered_set<int> set;
    // tuple
    auto tup = make_tuple(3.8, 'A', 100);
    cout << get<2>(tup) << endl;
    cout << get<double>(tup) << endl;
    double a;char b;int c;
    tie(a, b, c) = tup;
    auto tup2 = tuple_cat(tup, tup);
    print(tup2);
    cout<<tuple_len(tup2);
    return 0;
}

```

## 智能指针

```c++
#include <iostream>
#include <memory>

using namespace std;

void foo(shared_ptr<int> i) { ++(*i); }

int main() {
    // shared_ptr: reference count
    auto pointer = make_shared<int>(10);
    cout << *pointer << endl;
    foo(pointer);
    cout << *pointer << endl;
    auto pointer2 = pointer;
    ++(*pointer2);
    // disable sharing with other pointer
    unique_ptr<int> pointer3 = make_unique<int>(10);
    // auto pointer4 = pointer3; // wrong
    // check existence of pointer(doesn't increase count)
    weak_ptr<int> pointer4 = pointer;
    cout << pointer4.expired() << endl;
    return 0;
}

```

## 正则表达式

```c++
#include <iostream>
#include <string>
#include <regex>

using namespace std;

int main() {
    string fnames[] = {"foo.txt", "bar.txt", "test", "a0.txt", "AAA.txt"};
    regex reg("([a-z]+)\\.txt");
    smatch match;
    for (auto &fname: fnames) {
        cout << fname << ":" << regex_match(fname, reg) << endl; // match count
        if (regex_match(fname, match, reg)) // return bool, edit smatch
            if (match.size() == 2)
                cout << fname << " sub: " << match[1].str() << endl;
    }

    return 0;
}

```

## 语言级线程支持

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <future>
#include <queue>
#include <chrono>
#include <condition_variable>

using namespace std;
void foo() {
    cout << "hello world" << endl;
}
mutex mtx;

void block_area() {
    unique_lock<mutex> lock(mtx);
    //...
}

void conva() {
    // 生产者数量
    queue<int> produced_nums;
    // 互斥锁
    mutex m;
    // 条件变量
    condition_variable cond_var;
    // 结束标志
    bool done = false;
    // 通知标志
    bool notified = false;

    // 生产者线程
    thread producer([&]() {
        for (int i = 0; i < 5; ++i) {
            this_thread::sleep_for(chrono::seconds(1));
            // 创建互斥锁
            unique_lock<mutex> lock(m);
            cout << "producing " << i << '\n';
            produced_nums.push(i);
            notified = true;
            // 通知一个线程
            cond_var.notify_one();
        }
        done = true;
        cond_var.notify_one();
    });

    // 消费者线程
    thread consumer([&]() {
        unique_lock<mutex> lock(m);
        while (!done) {
            while (!notified) {  // 循环避免虚假唤醒
                cond_var.wait(lock);
            }
            while (!produced_nums.empty()) {
                cout << "consuming " << produced_nums.front() << '\n';
                produced_nums.pop();
            }
            notified = false;
        }
    });

    producer.join();
    consumer.join();

}

int main() {
    // create thread
    thread t(foo);
    t.join();
    // no other lock control same mutex
    thread thd(block_area);
    thd.join();
    // package the lambda exp in task
    // packaged_task template param is type of the func
    packaged_task<int()> task([](){return 7;});
    // can use future to get asych op result
    future<int> result = task.get_future();    // do task in one thread
    thread(move(task)).detach();    cout << "Waiting...";
    result.wait();
    cout << "Done!" <<  endl << "Result is " << result.get() << '\n';
    // awake waiting thread to avoid dead lock
    conva();
    return 0;
}
```

## 杂项/c++17

```c++
void no_except() noexcept;
string str = R"(C:\no\escape\character)";
#include <iostream>
#include <string>
#include <variant>
#include <tuple>

using namespace std;

// c++ 17 new feature
// non-type template parameter
template <auto value> void foo() {
    return;
}
// std::variant<>
template<typename T>
variant<T> fun(size_t i, T j) {
}
// structured binding
tuple<int,double,string> f() {
    return make_tuple(1,2.3,"456");
}
int main() {
    foo<10>();
    auto [x,y,z] = f();
    // can use temporary var in if/switch
    if (auto p = pair<int,int>(2,2); !p.second) {
        //...
    } else {
        //...
    }
    return 0;
}
```