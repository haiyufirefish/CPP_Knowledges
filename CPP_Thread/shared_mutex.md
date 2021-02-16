### The chapter is about std::shared_mutex
shared_mutex is introduced in C++ 14. It usually used in situations
when multiple reader can access the same resource at the time without
causing data races, but only one writer can do so.
**shared_lock** is read lock, accompanies with shared_mutex. The code can
be used by other thread.
**lock_guard and unique_lock** are write lock. Other thread can not access 
the code which is locked by them.
```
//usually the definitions are like:
typedef std::shared_lock<std::shared_mutex> Readlock;
typedef std::lock_guard<std::shared_mutex> Writelock;
```
The following code shows the performances between shared_mutex and mutex
idea from https://www.iteye.com/blog/aigo-2296462:
```
#include "stdafx.h"
#include <mutex>
#include <shared_mutex>
#include <thread>
#include <list>
#include <iostream>
#include <vector>

#define READ_THREAD_COUNT 8  
#define LOOP_COUNT 5000000  

typedef std::shared_lock<std::shared_mutex> ReadLock;
typedef std::lock_guard<std::shared_mutex> WriteLock;
typedef std::lock_guard<std::mutex> NormalLock;

class shared_mutex_counter {
public:
    shared_mutex_counter() = default;

    unsigned int get() const {
        ReadLock lock(mutex);
        return value;
    }

    void increment() {
        WriteLock lock(mutex);
        value++;
    }

private:
    mutable std::shared_mutex mutex;
    unsigned int value = 0;
};

class mutex_counter {
public:
    mutex_counter() = default;

    unsigned int get() const {
        NormalLock lock(mutex);
        return value;
    }

    void increment() {
        NormalLock lock(mutex);
        value++;
    }

private:
    mutable std::mutex mutex;
    unsigned int value = 0;
};

class timers
{
public:
    timers()
    {
        m_begin = std::chrono::high_resolution_clock::now();
    }

    ~timers()
    {
        m_end = std::chrono::high_resolution_clock::now();
        Consuming();
    }

    void Consuming()
    {
        std::cout << "Time-consuming:" << std::chrono::duration_cast<std::chrono::duration<float, std::milli>>(m_end - m_begin).count() << std::endl;
    }

private:
    std::chrono::high_resolution_clock::time_point m_begin;
    std::chrono::high_resolution_clock::time_point m_end;
};

void test_shared_mutex()
{
    shared_mutex_counter counter;
    unsigned int temp;

    auto writer = [&counter]() {
        for (unsigned int i = 0; i < LOOP_COUNT; i++){
            counter.increment();
        }
    };

    auto reader = [&counter, &temp]() {
        for (unsigned int i = 0; i < LOOP_COUNT; i++) {
            temp = counter.get();
        }
    };

    std::cout << "----- shared mutex test ------" << std::endl;
    std::list<std::shared_ptr<std::thread>> threadlist;
    {
        timers timer;

        for (int i = 0; i < READ_THREAD_COUNT; i++)
        {
            threadlist.push_back(std::make_shared<std::thread>(reader));
        }
        std::shared_ptr<std::thread> pw = std::make_shared<std::thread>(writer);

        for (auto &it : threadlist)
        {
            it->join();
        }
        pw->join();
    }
    std::cout <<"count:"<< counter.get() << ", temp:" << temp << std::endl;
}

void test_mutex()
{
    mutex_counter counter;
    unsigned int temp;

    auto writer = [&counter]() {
        for (unsigned int i = 0; i < LOOP_COUNT; i++) {
            counter.increment();
        }
    };

    auto reader = [&counter, &temp]() {
        for (unsigned int i = 0; i < LOOP_COUNT; i++) {
            temp = counter.get();
        }
    };

    std::cout << "----- mutex test ------" << std::endl;
    std::list<std::shared_ptr<std::thread>> threadlist;
    {
        timers timer;

        for (int i = 0; i < READ_THREAD_COUNT; i++)
        {
            threadlist.push_back(std::make_shared<std::thread>(reader));
        }

        std::shared_ptr<std::thread> pw = std::make_shared<std::thread>(writer);

        for (auto &it : threadlist)
        {
            it->join();
        }
        pw->join();
    }
    std::cout << "count:" << counter.get() << ", temp:" << temp << std::endl;
}

int main()
{
    test_shared_mutex();
    test_mutex();
    return 0;
}

/*----- shared mutex test ------
Time-consuming:4997.39
count:5000000, temp:1725302
----- mutex test ------
Time-consuming:9831.86
count:5000000, temp:5000000*/
```
shared_mutex using case is special: one or more thread read the shared data
simultaneously and only one thread can modify the resources. In this situation
shared_mutex can get the better performance.
