### The condition_variable class is a syncrhonization primitive 
It can be used to block a thread, or multiple threads at the same time, until another
thread both modifies a shared variable(the **condition**), and **notifies** the 
condition_variable.
- acquirea a std::mutex(typically via std::lock_guard)
- perform the modification while the lock is held
- execute notify_one or notify_all on the std::condition_variable

It only works with std::unique_lock<std::mutx>.
And std::condition_variable_any works with other normal lock object,such
as std::shared_lock.
```
// conditional_var.cpp : This file contains the 'main' function. Program execution begins and ends there.
//

#include <iostream>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex m;
std::condition_variable cv;
std::string data;
bool ready = false;
bool processed = false;

void worker_thread() 
{
    //Wait until main() sends data 
    std::unique_lock<std::mutex>lk(m);
    cv.wait(lk, [] {return ready; });

    //after the wait , we own the lock
    std::cout << "Worker thread is processing data\n";
    data += "after processing";

    //send data back to main()
    processed = true;
    std::cout << "Worker thread signals data processing completed \n";

    lk.unlock();
    cv.notify_one();
}
int main()
{
    std::thread worker(worker_thread);

    data = "Example data";

    //send data to worker thread
    {
        std::lock_guard<std::mutex> lk(m);
        ready = true;
        std::cout << "main() signals data ready for processing\n";
    }
    cv.notify_one();

    //wait for the worker
    {
        std::unique_lock<std::mutex> lk(m);
        cv.wait(lk, [] {return processed; });

    }
    std::cout << "Back in main(),data = " << data << '\n';

    worker.join();
}
```
An example how does std::unique_lock work:
```
#include <iostream>           // std::cout
#include <thread>             // std::thread
#include <mutex>              // std::mutex, std::unique_lock
#include <condition_variable> // std::condition_variable

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void print_id(int id) {
    std::unique_lock<std::mutex> lck(mtx);
    while (!ready) cv.wait(lck);
    // ...
    std::cout << "thread " << id << '\n';
}
void print_id_n(int id) {
    std::cout << "thread " << id << '\n';
}
void go() {
//without this line t11 with conpete with other threads.
    std::unique_lock<std::mutex> lck(mtx);
    std::thread t11(print_id_n, 11);
    
    ready = true;
    cv.notify_all();
    t11.join();
    
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
    for (auto& th : threads) th.join();
   
    return 0;
}
```

 