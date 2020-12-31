## function object is a general broad concept
It is a high-level abstract. It doesn't care about what is the object,
but just care about how does it work: it functional behavior.
```
function(arg1,arg2,...); //call function
```
lambda is a special function expression, it is actually an instance of 
a classs:
```
class{
public: 
	ReturnType operator()(params)const;
};

X f;
f(arg1,arg2); //equals to f.operator()(arg1,arg2);
```
The following function print an integer:
```
template<typename T>
class Print
{
public:
	void operator()(T elem)const
	{
		cout<< elem<<'';
	}
};

int main()
{
	vector<int> v(10);
	int init = 0;
	std::generate(v.begin(),v.end(),[&init]{return init++;});
	
	std::for_each(v.begin(),v.end(),Print<int>{});
	
	return 0;
}
```
This Print<int> is function object. STL algorithm is implemented generically.
It doesn't care about the type of accepted object, but it must support funtion
call.
```
//for_each implementation may differ
namespace std
{
	template<typename Iterator,typename Operation>
	Operation for_each(Iterator act,Iterator end, Operation op)
	{
		while(act !=end)
		{
			op(*act);
			++act;
		}
		return op;
	}
}
```
In essen, function object is class object, This also makes function objects have their own 
unique advantages over ordinary functions:\
**The function object is stateful**:Function objects are "smart functions" relative to 
ordinary functions, just like smart pointers compared to traditional pointers. 
Because function objects can have other methods and data members in addition to providing function caller methods.
**Each function object has its own type**:For ordinary functions, as long as the signatures are consistent, their 
types are the same. But this does not apply to function objects, because the type of a function object is the type 
of its class. In this way, function objects have their own types, which means that function objects can be used for 
template parameters, which greatly improves generic programming.
**function objects are normally faster than ordinary functions:**
Because function objects are generally used for template parameters, 
templates are generally optimized at compile time.
```
//this is a stateful function object
class IntSequence
{
public:
	IntSequence(int initVal) : value{ initVal}{}
	
	int operator()(){return ++value;}
private:
	int value;
};

int main()
{
	vector<int> v(10);
	std::generate(v.begin(),v.end(),IntSequence{0});
	/*lambda can achieve the same effect
		int init = 0;
		std::generate(v.begin(),v.end(),[&init]{return ++init;});
	*/
	std::for_each(v.begin(), v.end(), [](int x) { cout << x << ' '; });
	
	return 0;
}
```
The function object has a private member data. Each time calling it it will 
increase. And lambda must use reference catching. Function object can implement
more comlicated funciton:
```
class Meanvalue
{
public:
	Meanvalue():num{0},sum{0}{}
	
	void operator()(int e)
	{
		++num;
		sum +=num;
	}
	
	double value()
	{
		return static_cast<double>(sum)/static_cast<double>(num);}
	}
private:
	int num;
	int sum;
};

int main(0
{
	vector<int> v{1,3,5,7};
	Meanvalue mv = std::for_each(v.begin(),v.end(),Meanvalue{});
	cout<<mv.value<<endl;
}
```
If using lambda implement it, it will be a bit cumbersome. 
This can be regarded as a unique advantage of function objects.


