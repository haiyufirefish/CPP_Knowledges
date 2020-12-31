###In kontext of design pattern, the function binder is a special function object
In header <functional> defines the following function binders:
std::bind(op,arg..): bind function object op's parameter to special args.
std::mem_fn(op): transform a member function to a function object.
std::not1(op),std::not2(op): Unary and binary inverters
```
//if there are not parameter to bind, using std::placeholders::_1..
std::placeholders::_1,std::placeholders::_2
//example:
auto minus10 = std::bind(std::minus<int>{},std::placeholders::_1,10);
cout<<minus10(20)<<endl; //output 10
```
Sometimes binder can be used for sorting parameters:
```
auto vminus = std::bind(std::minus<int>{},std::placeholders::_2,std::placeholders::_1);
cout<<vminus(20,10)<<endl;//-10
```

Binders can also be nested with each other to achieve the 
combination of function objects:
```
auto plus10times2 = std::bind(std::multiplies<int>{},
	std::bind(std::plus<int>{},std::placeholders::_1,10),2);
	
cout<<plus10times2(4)<<endl;//28

auto pow3 = std::bind(std::multiplies<int>{},std::bind(std::multiplies<int>{},
std::placeholders::_1,std::placeholders:::_1),std::placeholders::_1);
cout<<pow3(3)<<endl;//27
```
Using different function object combinations, the function binder can call global 
functions. The following example is case-insensitive to determine whether a string 
contains a specific substring:
```
char myToupper(char c)
{
	if(c >= 'a' && c <= 'z')
		return static_cast<char>(c - 'a' + 'A');
	return c;
}

int main()
{
	string s{"Internationalization"};
	string sub{"Nation"};
	
	auto pos = std::search(s.begin(),s.end(),sub.begin(),sub.end(),
					std::bind(std::equal_to<char>{},
						std::bind(myToupper, std::placeholders::_1),
						std::bind(myToupper, std::placeholders::_2)));
	if(pos != s.end())
	{
		cout << sub << "is part of " <<s <<endl;
	}
	return 0;
}
```
Binder default uses pass-by-value to bind parameters,if pass-by-reference is
needed, using std::ref and std::cref. Former is normal reference and latter is
const reference binding:
```
void f(int& n1, int& n2, const int& n3)
{
	cout<< "In function: " <<n1 <<' '<<n2<<' '<<n3<<'\n';
	++n1;
	++n2;
	//++n3 cannot be compiled
}

int main()
{
	int n1 = 1,n2 =2, n3 =3;
	auto boundf = std::bind(f, n1, std::ref(n2),std::cref(n3)));
	n1 = 10;
	n2 = 11;
	n3 = 12;
	cout<< "Before function: " <<n1 <<' ' <<n2<<' '<<n3<<'\n';
	// 10 11 12
	boundf();// in function : 1 11 12
	cout<< "After function: "<<n1 <<' ' <<n2<<' '<<n3<<'\n';
	// 10 12 12
	return 0;
}
```
As it can be seen that n1 is default bound with n1 = 1, this is only copying.
It doesn't change the value of n1. n3 is const bound, its value cannot be changed.
```
class Person
{
public:
	Person(const string& n):name{n}{}
	void print()const {cout<<name<<endl;}
	void print2(const string& prefix){cout<<prefix<<name<<endl;}
private:
	string name;
};

int main()
{
	vector<Person>p{Person{"Tick"},Person{"Trick}};
	std::for_each(p.begin(),p.end(),std::bind(&Person::print,std::placeholders::_1));
	
	std::for_each(p.begin(),p.end(),std::bind(&Person::print2,std::placeholders::_1,
	"Person: " ));
	
	return 0;
}
```
Not only the member functions can be bound, also the virtual function can be bound.
```
vector<int> data{1,2,3,4};
auto func = std::bind([](const vector<int>& data){cout<<data.size()<<endl;},
									std::move(data));
func();
cout<<data.size()<<endl;//0
```
Here it binder implements move syntax, as for left parameter, binding object is
pass-by-value. As for right parameter, binding parameter is move construction.
When calling member functions, std::mem_fn is also for it:
```
vector<Person> p{Person{"Tick"},Person{"Trick"}};
std::for_each(p.begin(),p.end(),std::mem_fn(&Person::print));
//output Trick Trick
Person n{"Bob"};
std::mem_fn(&Person::print2)(n,"Person: ");
//output Person: Bob
```
std::men_fn doesn't need to bind parameter, it is more easier to call member function.