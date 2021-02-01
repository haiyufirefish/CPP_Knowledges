The mutex class is a synchronization primitive that can be used
to protect shared data from being simultaneously accessed by multiple 
threads.
- A calling thread owns a mutex from the time that it successfully calls 
either lock or try_lock until it calls unlock.
- When a thread owns a mutex, all other threads will block (for calls to lock) or receive a 
false return value (for try_lock) if they attempt to claim ownership of the mutex.
- A calling thread must not own the mutex prior to calling lock or try_lock.
std::mutex is ususally not accesed directly: std::unique_lock, std::lock_guard or
std::scoped_lock manage locking in a more exception-safe manner.
```
#include <iostream>
#include <map>
#include <string>
#include <chrono>
#include <thread>
#include <mutex>

std::map<std::string, std::string> g_pages;
std::mutex g_pages_mutex;

void save_page(const std::string &url)
{
    // simulate a long page fetch
    std::this_thread::sleep_for(std::chrono::seconds(2));
    std::string result = "fake content";
 
    std::lock_guard<std::mutex> guard(g_pages_mutex);
    g_pages[url] = result;
}
 
int main() 
{
    std::thread t1(save_page, "http://foo");
    std::thread t2(save_page, "http://bar");
    t1.join();
    t2.join();
 
    // safe to access g_pages without lock now, as the threads are joined
    for (const auto &pair : g_pages) {
        std::cout << pair.first << " => " << pair.second << '\n';
    }
}
```
The above code from library shows that a map is protected by the mutex.
without the guard, the first site cannot be saved into map.
More concise example from https://nrecursions.blogspot.com/2014/08/mutex-tutorial-and-example.html
- Thread: persons
- mutex: the door handle
- lock: the person's hand
- resource: the phone
Any thread has to execute some lines of code which should not be modified by other thread at 
the same time, has to acquire a lock on a mutex.
```
std::mutex m;//you can use std::lock_guard if you want to be exception safe 
int i = 0;
void makeACallFromPhoneBooth()
{	
	//without this code, the result maybe all i equals to 0
	std::lock_guard<std::mutex> guard(m);
	std::cout<< i << "hello wife\n;
	i++;
	
	//alternative
	m.lock();
	...
	m.unlock();
}

int main(){

	std::thread man1(makeACallFromPhoneBooth);
    
    std::thread man2(makeACallFromPhoneBooth);
    
    std::thread man3(makeACallFromPhoneBooth);

    man1.join();//man1 finished his phone call and joins the crowd
    man2.join();//man2 finished his phone call and joins the crowd
    man3.join();//man3 finished his phone call and joins the crowd
    return 0;
}
```
Now where is the position of std::lock_guard.
When a lock_guard object is created, it attempts to take ownership of the mutex it is given. When control leaves the scope in which the lock_guard 
object was created, the lock_guard is destructed and the mutex is released. 
It is **non-copyable**.
mutex is automatically released when lock goes out of scope.
	