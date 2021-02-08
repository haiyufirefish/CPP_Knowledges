### move consturctor
- Initialization: T a = std::move(b); or T a(std::move(b));, where b is of type T
Move constructors typically "steal" the resources held by the argument 
(e.g. pointers to dynamically-allocated objects, file descriptors, TCP sockets, I/O streams, running threads, etc.) 
rather than make copies of them, and leave the argument in some valid but otherwise indeterminate state.

For copy constuctor,firstly a temporary object is created, and then that temporary object is used to assign the values to the object. 
Due to this the copy constructor is called several times and increases the **overhead** and decreases the computational 
power of the code. To avoid this overhead and make the code more efficient we use move constructors.

For move constructor, it moves the resources in the heap and it nulls out the pointer of the temporary object preventing 
more than one object to point to same memory location.
Example code:
```
#include <string>
#include <iostream>
#include <iomanip>
#include <utility>
#include <sstream>

struct A
{
    std::string s;
    int k;
    A() : s("test"), k(-1) { }
    A(const A& o) : s(o.s), k(o.k) { std::cout << "move failed!\n"; }
    A(A&& o) noexcept :
        s(std::move(o.s)),       // explicit move of a member of class type
        k(std::exchange(o.k, 0)) // explicit move of a member of non-class type
    {
        
            std::cout << "move ok \n"; 
    }
};

A f(A a)
{
    return a;
}

struct B : A
{
    std::string s2;
    int n;
    // implicit move constructor B::(B&&)
    // calls A's move constructor
    // calls s2's move constructor
    // and makes a bitwise copy of n
};

struct C : B
{
    ~C() { } // destructor prevents implicit move constructor C::(C&&)
};

struct D : B
{
    D() { }
    ~D() { }          // destructor would prevent implicit move constructor D::(D&&)
    D(D&&)  = default; // forces a move constructor anyway
};

int main()
{
    std::cout << "Trying to move A\n";
    A a1 = f(A()); // return by value move-constructs the target from the function parameter
    std::cout << "Before move, a1.s = " << std::quoted(a1.s) << " a1.k = " << a1.k << '\n';
    A a2 = std::move(a1); // move-constructs from xvalue
    std::cout << "After move, a1.s = " << std::quoted(a1.s) << " a1.k = " << a1.k << '\n';

    std::cout << "Trying to move B\n";
    B b1;
    std::cout << "Before move, b1.s = " << std::quoted(b1.s) << "\n";
    B b2 = std::move(b1); // calls implicit move constructor
    std::cout << "After move, b1.s = " << std::quoted(b1.s) << "\n";

    std::cout << "Trying to move C\n";
    C c1;
    C c2 = std::move(c1); // calls copy constructor

    std::cout << "Trying to move D\n";
    D d1;
    D d2 = std::move(d1);

    std::cout<<"-----------------------\n";
    std::stringstream ss;
    std::string in = "String with spaces, and embedded \"quotes\" too";
    std::string out;

    auto show = [&](const auto& what) {
        &what == &in
            ? std::cout << "read in     [" << in << "]\n"
            << "stored as   [" << ss.str() << "]\n"
            : std::cout << "written out [" << out << "]\n\n";
    };

    ss << std::quoted(in);
    show(in);
    ss >> std::quoted(out);
    show(out);

    ss.str(""); // clear the stream buffer
}
```
The above code shows how to define move constructor. 
Now comparing the move and copy constructor:
```
// C++ program without declaring the
// move constructor
#include <iostream>
#include <vector>
using namespace std;

// Move Class
class Move {
private:
	// Declaring the raw pointer as
	// the data member of the class
	int* data;

public:
	// Constructor
	Move(int d)
	{
		// Declare object in the heap
		data = new int;
		*data = d;

		cout << "Constructor is called for "
			<< d << endl;
	};

	// Copy Constructor to delegated
	// Copy constructor
	Move(const Move& source)
		: Move{ *source.data }
	{

		// Copying constructor copying
		// the data by making deep copy
		cout << "Copy Constructor is called - "
			<< "Deep copy for "
			<< *source.data
			<< endl;
	}

	// Destructor
	~Move()
	{
		if (data != nullptr)

			// If the pointer is not
			// pointing to nullptr
			cout << "Destructor is called for "
			<< *data << endl;
		else

			// If the pointer is
			// pointing to nullptr
			cout << "Desturctor is called"
			<< " for nullptr"
			<< endl;

		// Free the memory assigned to
		// data member of the object
		delete data;
	}
};

// Driver Code
int main()
{
	// Create vector of Move Class
	vector<Move> vec;

	// Inserting object of Move class
	
	//Constructor is called for 3
	//Constructor is called for 3
	//Copy Constructor is called - Deep copy for 3
	Move m(3);
	Move mm(m);
	
//Constructor is called for 10
//Constructor is called for 10
//Copy Constructor is called - Deep copy for 10
//Destructor is called for 10
//Constructor is called for 20
//Constructor is called for 20
//Copy Constructor is called - Deep copy for 20
//Constructor is called for 10
//Copy Constructor is called - Deep copy for 10
	vec.push_back(Move{ 10 });
	vec.push_back(Move{ 20 });
	return 0;
}

```
With move constructor it will reduce the overhead:
```
// C++ program with declaring the
// move constructor
#include <iostream>
#include <vector>
using namespace std;

// Move Class
class Move {
private:
	// Declare the raw pointer as
	// the data member of class
	int* data;

public:

	// Constructor
	Move(int d)
	{
		// Declare object in the heap
		data = new int;
		*data = d;
		cout << "Constructor is called for "
			<< d << endl;
	};

	// Copy Constructor
	Move(const Move& source)
		: Move{ *source.data }
	{

		// Copying the data by making
		// deep copy
		cout << "Copy Constructor is called -"
			<< "Deep copy for "
			<< *source.data
			<< endl;
	}

	// Move Constructor
	Move(Move&& source)
		: data{ source.data }
	{

		cout << "Move Constructor for "
			<< *source.data << endl;
		source.data = nullptr;
	}

	// Destructor
	~Move()
	{
		if (data != nullptr)

			// If pointer is not pointing
			// to nullptr
			cout << "Destructor is called for "
				<< *data << endl;
		else

			// If pointer is pointing
			// to nullptr
			cout << "Destructor is called"
				<< " for nullptr "
				<< endl;

		// Free up the memory assigned to
		// The data member of the object
		delete data;
	}
};

// Driver Code
int main()
{
	// Vector of Move Class
	vector<Move> vec;

	// Inserting Object of Move Class
	Constructor is called for 10
	//Move Constructor for 10
	//Destructor is called for nullptr
	//Constructor is called for 20
	//Move Constructor for 20
	//Constructor is called for 10
	//Copy Constructor is called - Deep copy for 10
	//Destructor is called for 10
	//Destructor is called for nullptr
	//Destructor is called for 10
	//Destructor is called for 20
	vec.push_back(Move{ 10 });
	vec.push_back(Move{ 20 });
	return 0;
}
```
Source: https://www.geeksforgeeks.org/move-constructors-in-c-with-examples/