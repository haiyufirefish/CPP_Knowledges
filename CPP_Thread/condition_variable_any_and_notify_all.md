### The difference between condition_variable and condition_variable_any
The condition_variable_any class is a generalization of std::condition_variable. 
Whereas std::condition_variable works only on std::unique_lock<std::mutex>, 
condition_variable_any can operate on any lock that meets the BasicLockable requirements.

-If the lock is std::unique_lock, std::condition_variable may provide better performance.
- condition_variable_any **cannot combine with lock_guard**, as lock_guard doesn't  have lock() function
```
	// false code
	std::lock_guard<std::mutex> lck(mtx);
    cva.wait(lck, [] {return ready; });
	
	//the source code of wait
	 template <class _Lock>
    void wait(_Lock& _Lck) noexcept /* terminates */ { // wait for signal
        {
            const shared_ptr<mutex> _Ptr = _Myptr; // for immunity to *this destruction
            lock_guard<mutex> _Guard{*_Ptr};
            _Lck.unlock();
            _Cnd_wait(_Mycnd(), _Ptr->_Mymtx());
        } // unlock
		
		//!!!
        _Lck.lock();
    }
```

### notify_one vs notify_all
If there are several threads blocked on the condition variable, notify_one() will
unblock only one thread, while notify_all() will unblock them all.
```
#include <iostream>           // std::cout
#include <thread>             // std::thread
#include <mutex>              // std::mutex, std::unique_lock
#include <condition_variable> // std::condition_variable

std::mutex mtx;
std::condition_variable cv;
std::condition_variable_any cva;
bool ready = false;
bool ready_sec = false;
void print_id(int id) {
    std::unique_lock<std::mutex> lck(mtx);
    cva.wait(lck, [] {return ready_sec; });
    // ...
    std::cout << "thread " << id << '\n';
}
void print_id_n(int id) {
    std::cout << "thread " << id << '\n';
}
void go() {
    std::unique_lock<std::mutex> lck(mtx);
    std::thread t11(print_id_n, 11);
    
    ready = true;
    cva.notify_one();
    t11.join();
    
}
void go_to() {
    std::unique_lock<std::mutex> lck(mtx);
    std::cout << "here is go_to\n";
    cva.wait(lck, [] {return ready; });
    ready_sec = true;
    cva.notify_all();
    
}

int main()
{
    std::thread threads[10];
    // spawn 10 threads:
    for (int i = 0; i < 10; ++i)
        threads[i] = std::thread(print_id, i);

    std::cout << "10 threads ready to race...\n";
                               // go!
  
    go();
    go_to();
    for (auto& th : threads) th.join();
   
    return 0;
}
```	