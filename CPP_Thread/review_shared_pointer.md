### shared pointer
It can be used for function objects.
```
//it calls the two functions here
void del(void(*)()) {}
void fun() {}
int main(){
  std::shared_ptr<void()> ee(fun, del);
  (*ee)();
}
```
- the last remaining shared_ptr owning the object is destroyed;
- the last remaining shared_ptr owning the object is assigned another pointer via operator= or reset().

```
#include <iostream>
#include <memory>
#include <thread>
#include <chrono>
#include <mutex>

struct Base
{
    Base() { std::cout << "  Base::Base()\n"; }
    // Note: non-virtual destructor is OK here
    ~Base() { std::cout << "  Base::~Base()\n"; }
};

struct Derived : public Base
{
    Derived() { std::cout << "  Derived::Derived()\n"; }
    ~Derived() { std::cout << "  Derived::~Derived()\n"; }
};

void thr(std::shared_ptr<Base> p)
{
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout <<"pg---" <<p.get() << std::endl;
    std::shared_ptr<Base> lp = p; // thread-safe, even though the
                                  // shared use_count is incremented
    {
        static std::mutex io_mutex;
        std::lock_guard<std::mutex> lk(io_mutex);
        std::cout << "local pointer in a thread:\n"
            << "  lp.get() = " << lp.get()
            << ", lp.use_count() = " << lp.use_count() << '\n';
    }
}

int main()
{
    std::shared_ptr<Base> p = std::make_shared<Derived>();// always use this way to get shared_ptr
    std::cout << "----------------- = " << p.get() << std::endl;
    std::cout << "Created a shared Derived (as a pointer to Base)\n"
        << "  p.get() = " << p.get()
        << ", p.use_count() = " << p.use_count() << '\n';
    std::thread t1(thr, p), t2(thr, p), t3(thr, p);
    std::cout<< " -- p.get()-- = " << p.get()<<std::endl;
    p.reset(); // release ownership from main
    std::cout << "Shared ownership between 3 threads and released\n"
        << "ownership from main:\n"
        << "  p.get() = " << p.get()
        << ", p.use_count() = " << p.use_count() << '\n';
    t1.join(); t2.join(); t3.join();
    std::cout << "All threads completed, the last one deleted Derived\n";
}
```
The line 59 p = 0, but it has already assigned to t1,t2 and t3 in line 57.
To avoid circular reference: using weak_ptr<T>
```
std::shared_ptr<int> sh = std::make_shared<int>();
// shared_ptr initilization
std::weak_ptr<int> w(sh);
// get the shared_ptr
std::shared_ptr<int> another = w.lock();
// judge weak_ptr observed shared_ptr resources release or not 
bool isDeleted = w.expired();
```