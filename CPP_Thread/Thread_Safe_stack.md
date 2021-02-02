### This article is about a thread safe data structure stack
- stack: LIFO. 
It has functions: bool top(), void pop(), bool isempty(), void push(T ele),int size(),
T peek(), bool isFull();
```
#include <iostream>
#include <cstdlib>
using namespace std;

// define default capacity of the stack
#define SIZE 10

// Class for stack
template<typename T>
class stack
{
    T* arr;
    int top;
    int capacity;

public:
    stack(int size = SIZE);     // constructor
                     // destructor

    void push(T ele);
    T pop();
    T peek();

    int size();
    bool isEmpty();
    bool isFull();

    ~stack() {
        delete[] arr;
    }
};

template<typename T>
stack<T>::stack(int size)
{
    arr = new T[size];
    capacity = size;
    top = -1;
}

template<typename T>
void stack<T>::push(T x)
{
    if (isFull())
    {
        cout << "OverFlow\nProgram Terminated\n";
        exit(EXIT_FAILURE);
    }

    cout << "Inserting " << x << endl;
    arr[++top] = x;
}

template<typename T>
T stack<T>::pop()
{
    // check for stack underflow
    if (isEmpty())
    {
        cout << "UnderFlow\nProgram Terminated\n";
        exit(EXIT_FAILURE);
    }   
    cout << "Removing " << peek() << endl;
    return arr[top--];
    // decrease stack size by 1 and (optionally) return the popped element

}

template <typename T>
T stack<T>::peek()
{
    if (!isEmpty())
        return arr[top];
    else
        exit(EXIT_FAILURE);
}

template <typename T>
bool stack<T>::isEmpty()
{
    return top == -1;    // or return size() == 0;
}

template <typename T>
int stack<T>::size()
{
    return top + 1;
}


template <typename T>
bool stack<T>::isFull()
{
    return top == capacity - 1;    // or return size() == capacity;
}

int main()
{
    stack<string> pt(2);

    pt.push("A");
    pt.push("B");

    pt.pop();
    pt.pop();

    pt.push("C");

    // Prints the top of the stack
    cout << "Top element is: " << pt.peek() << endl;

    // Returns the number of elements present in the stack
    cout << "Stack size is " << pt.size() << endl;

    pt.pop();

    // check if stack is empty or not
    if (pt.isEmpty())
        cout << "Stack Is Empty\n";
    else
        cout << "Stack Is Not Empty\n";

    return 0;
}
```
And how about thread safe stack? Which properties should this stack have?
- For two threads, which using the same stack at the same time, it should have 
a mutex lock for that.
```
#include <exception>
#include <iostream>
#include <thread>
#include <mutex>
#include <memory>
#include <stack>
#include <string>

struct empty_stack :std::exception
{
	const char* what() const throw();
};

template<typename T>
class threadsafe_stack
{
private:
	std::stack<T> data;
	mutable std::mutex m;

public:
	threadsafe_stack() {}

	threadsafe_stack(const threadsafe_stack& other)
	{
		std::lock_guard<std::mutex>lock(other.m);
		data = other.data;
	}

	threadsafe_stack& operator=(const threadsafe_stack&) = delete;

	void push(T new_value)
	{
		std::lock_guard<std::mutex> lock(m);
		data.push(new_value);
	}

	std::shared_ptr<T> pop()
	{
		std::lock_guard<std::mutex> lock(m);
		if (data.empty()) throw empty_stack();
		std::shared_ptr<T> const res(std::make_shared<T>(data.top()));
		data.pop();
		return res;
	}

	void pop(T& value)
	{
		std::lock_guard<std::mutex>lock(m);
		if (data.empty())throw empty_stack();
		value = data.top();
		data.pop();
	}

	bool empty() const
	{
		std::lock_guard<std::mutex>lock(m);
		return data.empty();
	}

	T peek() {
		std::lock_guard<std::mutex>lock(m);
		return data.top();
	}
};
threadsafe_stack<std::string> st;
void addelement(std::string s) {
	st.push(s);
}
```
