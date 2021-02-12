join() is simple and brute force way to handle the thread,
either waiting for a thread to finish or not.
When using join(), the main thread terminates, waiting for called thread.Then the 
main thread recycles the called resouces and continues.
When using detach(),calling thread is running in background. The main function can
not get the control of it.Sometimes main() if finishing but calling thread is not 
finishing.
To avoid accidental lifetime problem:
```
struct func;
void f()
{
int some_local_state=0;
func my_func(some_local_state);
std::thread t(my_func);
try
{
	do_something_in_current_thread();
}
catch(...)
{
	t.join();
}
	t.join();
}
```
The thread::detach() is called, the thread of execution is "detached" from the thread object and is no longer represented by a thread object - they are two independent things.
### RAII(Resource Acquisition Is Initialization)
Check if a thread can join, using joinable:
```
void foo(){
	std::this_thread::sleep_for(std::chrono::seconds(1));
}

int main(){
	std::thread t;
	std::cout<<t.joinable()<<std::endl;//0
	t = std::thread(foo);
	std::cout<<t.joinable()<<std::endl;//1
	t.join();
	std::cout<<t.joinable()<<std::endl;//0
	
}
```
This example shows naiive implementation of accumulate, which restricts the thread numbers
```
template<typename Iterator,typename T>
struct accumulate_block
{
	void operator()(Iterator first,Iterator last,T& result)
{
	result=std::accumulate(first,last,result);
}
};
template<typename Iterator,typename T>
T parallel_accumulate(Iterator first,Iterator last,T init)
{
	unsigned long const length=std::distance(first,last);
	if(!length)
	return init;
	unsigned long const min_per_thread=25;
	unsigned long const max_threads=
	length+min_per_thread-1)/min_per_thread;
	unsigned long const hardware_threads=
	std::thread::hardware_concurrency();
	unsigned long const num_threads=
	std::min(hardware_threads!=0?hardware_threads:2,max_threads);
	unsigned long const block_size=length/num_threads;
	std::vector<T> results(num_threads);
	std::vector<std::thread> threads(num_threads-1);
	Iterator block_start=first;
	for(unsigned long i=0;i<(num_threads-1);++i)
	{
	Iterator block_end=block_start;
	std::advance(block_end,block_size);
	threads[i]=std::thread(
	accumulate_block<Iterator,T>(),
	block_start,block_end,std::ref(results[i]));
	block_start=block_end;
	}
	accumulate_block<Iterator,T>()(
	block_start,last,results[num_threads-1]);
	std::for_each(threads.begin(),threads.end(),
	std::mem_fn(&std::thread::join));
	return std::accumulate(results.begin(),results.end(),init);
}

```

