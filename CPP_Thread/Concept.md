At the simplest and most basic level, concurrency is about two or more separate activities
happening at the same time. A single system performing
multiple independent activities in parallel, rather than sequentially, or one
after the other.
Threads are much like lightweight processes: each thread runs independently of
the others, and each thread may run a different sequence of instructions.
But all threads in a process share the same address space, and most of the data can be
accessed directly from all threads¡ªglobal variables remain global, and pointers or references
to objects or data can be passed around among threads.
### The two reasons using concurrency:separation of concerns and performance
For performance: there are two approaches: **data parallelism** and task parallelism.

### when to use parallelism?
Fundamentally, the only reason not to use concurrency is when the benefit is not
worth the cost. Code using concurrency is harder to understand in many cases, so
there¡¯s a direct intellectual cost to writing and maintaining multithreaded code, and
the additional complexity can also lead to more bugs.
Use of concurrency for performance is just like any other optimization strategy: it
has potential to greatly improve the performance of your application, but it can also
complicate the code, making it harder to understand and more prone to bugs.
```
//the basic way to run a thread
void hello() {
    cout << "hello w\n";
}
int main()
{
    std::thread t(hello);
    t.join();
}
//just like this form
void do_some_work();
std::thread my_thread(do_some_work);

```
For class, passing thread is in this way:
```
class background_task
{
public:
    void operator()() const
    {
        cout << "this is constructor1\n";
        cout << "this is constructor2\n";
    }
};
int main(){
    background_task f;
	//uses the new uniform initialization syntax with braces rather
	//than parentheses, and thus would also declare a variable.
    std::thread my_thread{ background_task() };
	
	//the extra parentheses prevent interpretation as a function
    //declaration, thus allowing my_thread to be declared as a variable of type std::thread.
	//std::thread my_thread( background_task());
	//std::thread my_thread( f);
    my_thread.join();
}

//lambda form:

std::thread my_thread([](
do_something();
do_something_else();
});
```
detach: Separates the thread of execution from the thread object, 
allowing execution to continue independently. Any allocated resources will be freed once the thread exits.
The following code show the dangling reference:
```
struct func
{
    int& i;
    func(int& i_) :i(i_) {}

    void operator()()
    {
        for (unsigned j = 0; j < 1000; ++j)
        {
            cout << "j equals to: " << j << '\n';
            ++i;
            cout << "i equals to: " << i << '\n';//different from j, i may equal to: -858992624(strange number)
        }
    }
};

void oops()
{
    int some_local_state = 0;
    func my_func(some_local_state);
    std::thread my_thread(my_func);
    my_thread.detach();
    //my_thread.join();
}
//if the there is not other thread, it runs normally
```
