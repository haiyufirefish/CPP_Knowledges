### The difference between std::unique_lock and std::lock_guard
std::lock_guard will be locked only once on construction and **unlocked 
on destruction**. For the use case, which uses condition_variable, needs 
a unique_lock.
- unique_lock has other features that allow it to be constructed without locking 
the mutex immediately but to build the **RAII wrapper**.
```
//in this way
std::unique_lock<std::mutex> lk1(mutex1, std::defer_lock);
std::unique_lock<std::mutex> lk2(mutex2, std::defer_lock);
std::lock(lk1, lk2);
```
- std::lock_guard also provides a convenient RAII wrapper, but cannot lock multiple 
mutexes **safely**. Cases are from [here](https://stackoverflow.com/questions/20516773/stdunique-lockstdmutex-or-stdlock-guardstdmutex)
```
class MyClass
{
	std::mutex my_mutex;
	void memer_foo()
	{
		std::lock_guard<mutex_type> lock(this->my_mutex);
		/* block of code which needs mutual exclusion
		e.g open the file in multiple thread
		*/
		//mutex is automatically released when lock goes out of scope
	}
};
```
- std::unique_lock might have a tad more overhead.
- Since C++17, it uses std::scope_lock instead of std::lock