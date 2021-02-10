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

