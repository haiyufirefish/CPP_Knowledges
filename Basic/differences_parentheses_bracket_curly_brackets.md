### The C++ has servarals initilization ways
- case 1 bracket and curly brackets
It depends on the initilizer
```
#include <initializer_list>
#include <iostream>
using namespace std;

struct Test2 {
    Test2(initializer_list<int> l) {}
	//if Test2(){}here no error
};

int main() {
    Test2* test3 = new Test2(); // compile error: no default ctor
    Test2* test4 = new Test2{}; // calls initializer_list ctor
}

```

- case 2 (v) and {v}
```
struct Value {
};

struct Test3 {
    Test3(Value v) {}
    Test3(initializer_list<Value> v) {}
};

int main() {
    Value v;
    Test3* test5 = new Test3(v); // calls Test3(Value)
    Test3* test6 = new Test3{v}; // calls Test3(initializer_list<Value>)
}
//for vector:
using Vec = std::vector<int>;
Vec* v1 = new Vec(10);//vector size of 10 holds ten 0
Vec* v2 = new Vec{10};//vector size of 1 holds 10

```
If the value is not input there is no difference
```
#include <initializer_list>
#include <iostream>
using namespace std;

struct Test {
    Test() {}
    Test(initializer_list<int> l) {}
};

int main() {
    Test* test1 = new Test(); // calls default ctor
    Test* test2 = new Test{}; // same, calls default ctor
}
```
