### thread-safe push without locks
```
class lock_free_stack{
private:
	struct node{
		T data;
		node* next;
		node(T const& data_):
			data(data_)
		{}
};
	std::atomic<node*>head;
public:
	void push(T const& data)
	{	
		node* const new_node = new node(data);
		//exception only happenes here: construction of new node
		new_node->next = head.load();
		//prevents problematic race condition
		while(!head.compare_exchange_weak(new_node->next,new_node));
	}
};
```
Once it succeeds, the node is on the list avaiable to others.
For retrieving an item(pop)
- 1.Read the current value of head
- 2.read head->next
- 3.set head to head->next
- 4.return the data from the retrieved node
- 5.delete the retrieved node
Changllenges: two threads remove items from the stack may read the same value of
head at step 1. If one proceeds to step 5 before the other completes step 2, the other
will deference a dangling pointer.
And also two might return the same node: one proceeds to 4 and the other proceeds.
```
void pop(T& result
{
	node* old_head head.load();

	while(
	!head.compare_exchange_weak( old_head,old_head->next))
	result = old_head -->data;
}
```
But the above can not deal with empty list.
Using smart pointer:
```
struct node{	
	std::shared_ptr<T> data;
	node* next;
	node(T const& data_):data(std::make_shared<T>(data_))
	{}
};
std::atomic<node*>head;

void push(T const& data)
{
	node* const new_node = new node(data);
	new_node -> next = head.load();
	while(!head.compare_exchange_weak(new_node->next,new_node));
}

std::shared_ptr<T> pop()
{	
	//
	node* old_head = head.load();
	while(old_head && !head.compare_exchange_weak(old_head,old_head->next));
	return old_head ? old_head -> data: std::shared_ptr<T>();
}
```
The above code cannot solve the memory leak problem. Node can only be deleted
once **no other thread holds pointer to it**.
Need to write garbage collector just for nodes
- if none calls pop(), all nodes awaiting deletion can be deleted
- How to keep track? -> Count the number of threads executing pop()



