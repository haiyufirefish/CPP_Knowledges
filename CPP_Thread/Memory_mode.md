### Memory models basics
There are two aspects to the memory model: the basic structural aspects, which relate to how things are laid
out in memory, and then **concurrency** aspects.
If you use bit fields, this is an important point to note: though adjacent bit fields are distinct objects, they’re still
counted as the same memory location.
- Adjacent bit fields are part of the same memory location.
- Every variable is an object, including those that are members of other objects.
- Every object occupies at least one memory location.
For multithreading applications in C++: everything hings on memory locations. If two threads access the same memory location,
it needs to be carefeul.In order to avoid the race condition, there has to be an **enforced ordering**
between the accesses in the two threads.
- One way is to use mutex.
- Another way is way is to use the properties of atomic operations 
The latter is on the same or other memory locations to **enforce an ordering** between the accesses in the two threads.
If more than two threads access the same memory location, each pair of accesses must have a **defined ordering**.
**undefined behavior is one of the nastiest corners of C++**: If there’s no enforced ordering between two accesses to a single memory location
from separate threads, one or both of those accesses is not atomic, and one or both is
a write, then this is a data race and causes undefined behavior.
- use atomic operations, the compiler is responsible for ensuring that the necessary
synchronization is in place

If the load operation that reads the value of an object is atomic, and all modifications to that object are
also atomic, that load will retrieve either the initial value of the object or the value stored by one of the modifications.

The standard atomic types are not copyable or assignable in the conventional
sense, in that they have no copy constructors or copy assignment operators.

But it supportes assignment from and implicit conversion to the corresponding
built-in types as well as direct load() and store() member functions, exchange(),
compare_exchange_weak(), and compare_exchange_strong().

Each of the operations on the atomic types has an optional memory-ordering argument
that can be used to specify the required memory-ordering semantics.
- Store operations: memory_order_relaxed, memory_order_release,
or memory_order_seq_cst ordering
- Load operations: memory_order_relaxed, memory_order_consume,
memory_order_acquire, or memory_order_seq_cst ordering
- Read-modify-write: memory_order_relaxed, memory_
order_consume, memory_order_acquire, memory_order_release, memory_order_
acq_rel, or memory_order_seq_cst ordering
**The default ordering for all operations is memory_order_seq_cst.**

std::atomic_flag: It is intended as a building block only and must be initialized with ATOMIC_FLAG_INIT.
The following example shows that atomic_flag is used as a spinlock mutex.
```
//example from dev
#include <thread>
#include <vector>
#include <iostream>
#include <atomic>
 
std::atomic_flag lock = ATOMIC_FLAG_INIT;
 
void f(int n)
{
    for (int cnt = 0; cnt < 100; ++cnt) {
        while (lock.test_and_set(std::memory_order_acquire))  // acquire lock
             ; // spin
        std::cout << "Output from thread " << n << '\n';
        lock.clear(std::memory_order_release);               // release lock
    }
}
 
int main()
{
    std::vector<std::thread> v;
    for (int n = 0; n < 10; ++n) {
        v.emplace_back(f, n);
    }
    for (auto& t : v) {
        t.join();
    }
}

std::cout<<"-----------------------basic spinlock------------------------------------\n";
class spinlock_mutex
{
	std::atomic_flag flag;
public:
	spinlock_mutex():
	flag(ATOMIC_FLAG_INIT)
	{}
void lock()
{
	while(flag.test_and_set(std::memory_order_acquire));
}

void unlock()
{
flag.clear(std::memory_order_release);
}
};
```
Differences between memory_exchange_weak and memory_exchange_strong:
- strong always check for spurious failure and mask it 
- weak is used in loop(better performance) and strong for non-loop.

Store operation:
- can use memory_order_relaxed, memory_order_release,
or memory_order_seq_cst ordering

Load operations:
- can have memory_order_relaxed, memory_order_consume,
memory_order_acquire, or memory_order_seq_cst ordering

Read-modify-write operations:
- can have memory_order_relaxed, memory_
order_consume, memory_order_acquire, memory_order_release, memory_order_
acq_rel, or memory_order_seq_cst ordering
