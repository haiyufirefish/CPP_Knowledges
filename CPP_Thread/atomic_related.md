### This article is about std::atomic and its applications
Each instantiation and full specialization of std::atomic<> represents a type
that different threads can simultanesouly operate on(their instances), without 
raising undefined behavior:
- Objects of atomic types are the only C++ objects that free from data races
That is, if one thread writes to an atomic object while another thread reads 
from it, the behavior is well-defined.
- Accesses to atomic objects may establish inter-thread synchronization and order
non-atomic memory accesses as specified by std::memory_order

The first thread set the data ready, the second thread doesn't read the data until flag is set.
```
#include <vector>
#include <atomic>
#include <iostream>
std::vector<int> data;
std::atomic<bool> data_ready(false);
void reader_thread()
{
while(!data_ready.load()) //1
{
std::this_thread::sleep(std::milliseconds(1));
}
std::cout<< "The answer= "<<data[0]<<'\n'; //2
}
void writer_thread()
{
data.push_back(42);    //3
data_ready=true;	   //4
}
```
The required enforced ordering comes from atomic<bool> variable data_ready
- Memory model relationship: happens-before and synchronizes-with
The ordering is 1->3->4->1->2

### The synchronizes-with relationship
The synchronizes-with relationship is something that you can get only between operations
on atomic types.Operations on a data structure (such as locking a mutex) might
provide this relationship if the data structure contains atomic types and the operations
on that data structure perform the appropriate atomic operations internally, but
fundamentally it comes only from operations on atomic types.

**The basic idea is this**: a suitably tagged atomic write operation W on a variable x synchronizes-
with a suitably tagged atomic read operation on x that reads the value
stored by either that write (W), or a subsequent atomic write operation on x by the
same thread that performed the initial write W, or a sequence of atomic read-modify write
operations on x (such as fetch_add() or compare_exchange_weak()) by any
thread, where the value read by the first thread in the sequence is the value written by W
