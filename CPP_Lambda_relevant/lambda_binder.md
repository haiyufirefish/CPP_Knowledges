From last chapter, lambda can be a return type, it can assigns value to 
function pointer. But it is not convenient to use function pointer.
In STL, it has polymorphy function object to wrap function:std::function
```
std::function<bool(int,int)>wrapper  = [](int x,int y){return x<y;};
```
The significant application of lambda is that it can recall function.
```
int value = 3;
vector<int> v{1,2,3,4,5,6,10};

int count = std::count_if(v.begin(),v.end(),[value](int x){return x>value;});
```
This is most frequenct case of lambda to count the values.
```
vector<int> v(10);
int a = 0;
int b = 1;
std::generate(v.begin(),v.end(),[&a,&b]{int value =	b;b = b+a;a = value; 
return value;});
//v:{1,1,2,3,5,8,13,21,34,55}

// Equivalent to std::iota(v.begin(), v.end(), 0);
std::generate(v.begin(), v.end(), [n = 0] () mutable { return n++; });
//v:{0,1,2,...}
```
Also lambda can be used for objects sort:
```
class Person:
{
public:
	Person(const string& first,const string& last):
		firstName{first},lastName{last}
		{}
	Person() = default;
	
	string first()const{return firstName;}
	string last() const {return lastName;}
	
private:
	string firstName;
	string lastName;
};

int main()
{
	vector<Person> vp;
	//add person information
	
	//order by name
	sort(vp.begin(),vp.end(),[](const Person& p1, const Person& p2)
	{	return p1.last() < p2.last() || (p1.last() == p2.last() &&
	p1.first()<p2.first());});
	}
	return 0;
}
```
For most of the STL algorithms, it can resilient use lambda expression to 
achieve goal:
```
[capture-list](params)mutable(optional)constexpr (optional)(c++17)
exception attribute -> ret{body}
[capture-list](params)->ret{body}
[capture-list](params){body}
[capture-list]{body}
```
constexpr: can point to lambda expression is const functor.
exception: can point to lambda can throw exception
attributte: point to lambda charateristic
body: executing body

New in C++14: one is lambda template, another is lambda can catch expression.
```
int x = 4;
auto y = [&r = x,x = x +1]{r+=2;return x*x;}();
cout << yb << endl; // 25 type of yb is int
cout << xb << endl;//6

cout<<yb<<endl;//25

auto z = [str = "string"] {return str;}();
//type of z is const char* 
```
Here catching right value and this value may not in its scope.
Some objects can not be copied but can move, but lambda expression can catch 
them.
```
auto mypi = std::make_unique<double>(3.1415);

auto circle_area = [pi = std::move(mypi)](double r){return *pi*r*r;};
cout<< circle_area(1.0)<<endl;
```
Template:
```
auto add = [](auto x, auto y){return x+y;};

int x = add(2,3);
double y = add(2.5,3.5);
```


	
