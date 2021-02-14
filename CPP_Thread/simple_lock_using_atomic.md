 ### using release-acquire mode can build a simple mutex
 ```
 #include <iostream>
#include <memory>
#include <thread>
#include <chrono>
#include <mutex>

# include<atomic>

using namespace std;

class simple_mutex {
public:
	simple_mutex(): flag(0) {}
	void lock() {
		int expected = 0;
		while (!flag.compare_exchange_strong(expected, 1, memory_order_acq_rel)) {
			cout << expected << endl;
			expected = 0;
		}

		cout << "--------"<< flag.load()<< endl;
	}

	void unlock() {
		flag.store(0,memory_order_release);
	}
private:
	atomic<int> flag;
};
simple_mutex m;
void p(int thread_num) {
	
	m.lock();
	for (int i = 0; i < 10; i++)
		cout << "thread_num is " << thread_num << " " << i << endl;
	m.unlock();
}

int main()
{	
	thread t1(p, 1);
	thread t2(p, 2);

	t1.join();
	t2.join();
 
}
```