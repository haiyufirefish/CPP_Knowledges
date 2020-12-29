### lambda is a powerful and useful tool since C++11
A easy lambda form:
```
auto plus10 = [](int i) {
		return i + 10;
	};
cout<<plus10(10)<<endl;//20
```
Generally speaking, lambda expression generates an function object itself.
In type, the operator() is overloaded. This object has behavior like function.
It is so called **Function Object or Functor**. Compared to lambda expression,
function object has its special strenght.

```
auto add = [](int a, int b)->int{return a+b;};
auto mutiply = [](int a, int b){return a*b;};

int sum = add(2,5);//output 7
int product = mutiply(2,5);//output 10
``
-> can refer to return type, it can be ignored.
(): closure. When a lambda expression is defined, compiler will generate an
anonymous class. During executing, this expression will return an anonymous 
closure instance. This is a right value.
The closure: It can catch variables in the encapsulation through reference or
pass-value.

```
int x = 10;

auto add_x = [x](int a){return a+x;};//copy catch x
auto mutiply_x = [&x](int a){return a*x;};//reference catch x

cout<< add_x(10) << " "<<mutiply_x(10)<<endl;

```
This class closure implemented operator overload.
```
class ClosureType
{
public:
	//..
	ReturnType operator(params) const {body};
}
```
It means that lambda cannot modify copying variables, as overloaded method is const.
If the passing value need to be modified, using **mutable**
```
auto add_x = [x](int a)mutable{x*=2;return a+x;};

cout<<add_x(10)<<endl;//30
cout<<x<<endl;//10

//if auto add_x = [&x](int a)mutable{x*=2;return a+x;};
//output 20. Easy to understand, here don't clarify anymore
```
Why this can happen?
```
class ClosureType{
public:
	//...
	ReturnType operator(params){body};//here overload const disappears
}
```
For pass-by-reference, whether if it contains mutable, the value can be modified.
Attention, lambda cannot be assigned value:
```
auto a = []{cout<<"A"<<endl;};
auto b = []{cout<<"B"<<endl;};

a = b;//illegal
auto c = a; //ok
```
For type of a the that of b are consisten, why they cannot assign?
```
//The operator= is banned.
ClosureType& operator = (const ClosureType&) = delete;
```
The catching parameters in the scope can be as following:
1. []: nothing.\
2. [=] default catch all parameters by values \
3. [&] catch all parameters by references\
4. [x] catch only variable x\
5. [&x] catch only reference of x\
6. [=,&x] catch all parameters by values except x, only by reference\
7. [&,x] catch all parameters by references, except x by value\
8. [this] copy pointer catch current object. \
9. [*this] catch current object by pass-by-value

The above catching, don't use [=] and [&], which may lead to **Dangling references**,
as reference catching will not prelong reference parameters period.
```
std::function<int(int)> add_x(int x){
return [&](int a){return x+a;}
}
```
Parameter is only a temporary variable, it will be destroyed after function call.
But the return lambda expression will call the x.
```
std::function<int(int)> add_x(int x)
{
	return [=](int a){return x+a;};
}
```
It can solve Dangling reference problem, but may causes other possibilities.
```
class Filter{
public:
	Filter(int divisorVal):
		divisor{divisorVal}
	{}
	
	std::function<bool(int)>getFilter()
	{
		return [=](int value) {return value% divisor == 0;};
	}
private:
	int divisor;
};
```
This expression catches divisor itself, not its copying.
For the divisor is private, it cannot be caught:
```
function<bool(int)>getFilter_()
	{
		return [divisor](int value) {return value % divisor == 0; };
	}
	
	//{
	//	return [this](int value) {return value % this->divisor == 0; };
	//}
```
In each class, it has this pointer variable.