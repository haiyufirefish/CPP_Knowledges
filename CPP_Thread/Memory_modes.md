### There are 6 memory ordering modes in CPP
```
    memory_order_relaxed,
    memory_order_consume,
    memory_order_acquire,
    memory_order_release,
    memory_order_acq_rel,
    memory_order_seq_cst
```	
generally speaking, there are just 4 modes:
namely Relaxed ordering, Release-Acquire ordering, Release-Consume ordering
Sequentially-consistent ordering.

- memory_order_seq_cst: Ordering consistent mode, the **default** and most consistent mode.
From the hardware perspective: It has the most overhead. Every CPUs has its cache. A variable
can be read from several cache. It needs to be synchronized when it is modified in one cache.
This involves the communications between CPUs.

- Relaxed ordering: In single thread, the operations of atomic is sequential
Which ordering: Code ordering(sequenced-before). The operations from two atomic:
ordering is arbitary. It doesn't ensure operations order.
```
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> value_x = 0;
std::atomic<int> value_y = 0;
int r1 = 0;
int r2 = 0;

void m1() {
     r1 = value_y.load(std::memory_order_relaxed);//A
    value_x.store(r1, std::memory_order_relaxed);//B
    
   
}

void m2() {
    r2 = value_x.load(std::memory_order_relaxed);//C
    value_y.store(42,std::memory_order_relaxed); //D
   
}

int main()
{
    std::thread t1(m1);
    std::thread t2(m2);

    t1.join();
    t2.join();

    std::cout << " r1 " << r1 << '\n';
    std::cout << " r2 " << r2 << '\n';  //confusing is that order D->A->B->C never happen
}
```
The typical application is that program timer:	
```
#include <cassert>
#include <vector>
#include <iostream>
#include <thread>
#include <atomic>
std::atomic<int> cnt = { 0 };
void f()
{
    for (int n = 0; n < 1000; ++n) {
        cnt.fetch_add(1, std::memory_order_relaxed);
    }
}
int main()
{
    std::vector<std::thread> v;
    for (int n = 0; n < 10; ++n) {
        v.emplace_back(f);
    }
    for (auto& t : v) {
        t.join();
    }
    assert(cnt == 10000);    // never failed
    
    return 0;
}
```

- Release -- acquire: The operations of atomic from different threads. How to define
their ordering. It needs two threads to be synchronize with for a variable read/write
operations. 
The operation: In thread A atomically write x(release), thread B read the x value(acquire).
It ensures that B can get the current value of x. Whenn thread B acquires x, each read operation
is visable. a and b have ordering relationship. It is so called happen-before.
How to use:store() uses memory_order_release, load uses memory_order_acquire
- the read/write operation before store(),cannot be moved after the store()
- the read/write operation after load(),cannot be moved before the load()
- The summary of above: The operations before store() of a is visiable for thread b
when thread b successful load().
```
#include <thread>
#include <atomic>
#include <cassert>
#include <string>
std::atomic<bool> ready{ false };
int data = 0;
void producer()
{
    data = 100;                                       // A
    ready.store(true, std::memory_order_release);     // B
}
void consumer()
{
    while (!ready.load(std::memory_order_acquire))    // C
        ;
    assert(data == 100); // never failed              // D
}
int main()
{
    std::thread t1(producer);
    std::thread t2(consumer);
    t1.join();
    t2.join();
    return 0;
}
```
A muss happend before B, D must happen before D
- Release -- consume: If only operation for x is needed, not the two thread. Using this way 
can reduce the extra cost. The tradeoff is that only B can see A has changed the value x.

- concept: carries dependency:
	S1.c = a +b;
	S2.e = c +d;
S2 is dependent from S1.
```
struct X
{
int i;
std::string s;
};
std::atomic<X*> p;
std::atomic<int> a;
void create_x()
{
X* x=new X;
x->i=42;
x->s="hello";
a.store(99,std::memory_order_relaxed);
p.store(x,std::memory_order_release);
}

void use_x()
{
X* x;
while(!(x=p.load(std::memory_order_consume)))
std::this_thread::sleep_for(
std::chrono::microseconds(1));
assert(x->i==42);//ok
assert(x->s=="hello");//ok
assert(a.load(std::memory_order_relaxed)==99);//maybe fire
}
int main()
{
std::thread t1(create_x);
std::thread t2(use_x);
t1.join();
t2.join();
}
```
The above code shows that dependency-ordered-before. variable x carries a 
dependency into p

From [IBM paper](https://www.researchgate.net/profile/Paul_Mckenney/publication/228824849_Memory_Barriers_a_Hardware_View_for_Software_Hackers/links/0deec51aad19050a3f000000/Memory-Barriers-a-Hardware-View-for-Software-Hackers.pdf?origin=publication_detail)
Loads are not reordered with other loads.
Stores are not reordered with other stores.
Stores are not reordered with older loads.
Loads may be reordered with older stores to different locations.
That means that store-load can be reloaded, so x86 structure is not ordering consistent.
But the other three read/write mode cannot be reordered. So X86 is **acquire/release** mode.

Meaning of memory model in C++ 11:
In high level language achieve the control of multithreading in multiprocessors.
The reason why C++ offering this 6 modes: define how program execute in a thread.
In order to solve the problems happen in multihreading enviroment.

Sometimes carrying a dependency causes unnecessary overhead
- prevents cretain compiler optimization
- std::kill_dependency can be used to explicitly break dependency chain
- Copies argument to return value
This may be used to avoid **unnecessary std::memory_order_acquire fences** when 
the dependency chain leaves function scope 

Release sequence: assures multiple threads can synchronize their loads on a single 
store.
```
#include <thread>
#include <atomic>
#include <cassert>
#include <vector>
#include <iostream>

std::vector<int> queue_data;
std::atomic<int> count;
void populate_queue()
{
    unsigned const number_of_items = 20;
    queue_data.clear();
    for (unsigned i = 0; i < number_of_items; ++i)
    {
        queue_data.push_back(i);
    }
    count.store(number_of_items,
        std::memory_order_release);
}
void process(int a) {
    std::cout << a << "\n";
}
void wait_for_more_items() {
    std::this_thread::sleep_for(
        std::chrono::microseconds(1));
}

void consume_queue_items() {
    while (true) {
        int item_index;
        if ((item_index = count.fetch_sub(1,
            std::memory_order_acquire)) <= 0) {
            wait_for_more_items();
            continue;
        }
        process(queue_data[item_index - 1]);
    }
}

int main() {
    std::thread a(populate_queue);
    std::thread b(consume_queue_items);
    std::thread c(consume_queue_items);
    a.join();
    b.join();
    c.join();
	//output 0-19 arbitary
}
```

Memory fence:
Establishes memory synchronization ordering of non-atomic and relaxed atomic accesses, as instructed by order, 
without an associated atomic operation.
```
std::atomic<bool> x,y;
std::atomic<int> z;
void write_x_then_y()
{
x.store(true,std::memory_order_relaxed);
std::atomic_thread_fence(std::memory_order_release);
y.store(true,std::memory_order_relaxed);
}

void read_y_then_x()
{
while(!y.load(std::memory_order_relaxed));
std::atomic_thread_fence(std::memory_order_acquire);
if(x.load(std::memory_order_relaxed))
++z;
}

int main()
{
x=false;
y=false;
z=0;
std::thread a(write_x_then_y);
std::thread b(read_y_then_x);
a.join();
b.join();
assert(z.load()!=0);
}
//if x is not atomic type: 
bool x=false;
std::atomic<bool> y;
std::atomic<int> z;
void write_x_then_y() {
x=true;
std::atomic_thread_fence(std::memory_order_release);
y.store(true,std::memory_order_relaxed);
}
void read_y_then_x() {
while(!y.load(std::memory_order_relaxed));
std::atomic_thread_fence(std::memory_order_acquire);
if(x)
++z;
}
```
The release fence sychronizes with the acquire fence
Effect of release fence as if the store to y was tagged as release
effect of acquire fence as if the load from y was tagged as acquire