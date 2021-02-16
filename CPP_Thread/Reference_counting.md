```
template<typdename T>
class lock_free_stack
{
private:
	std::atomic<unsigned> threads_in_pop;
	void try_reclaim(node* old_head);
	std::atomic<node*> to_be_deleted;
	ststic void delete_nodes(node* nodes)
	{
		while(nodes){
			node* next = nodes->next;
			delete nodes;
			nodes = next;
		}
	}
	void try_reclaim(node* old_head)
	{
		if(thread_in_pop == 1)
		{
			node* nodes_to_delete = to_be_deleted.exchange(nullptr);
			if(!--threads_in_pop)
			{
				delete_nodes(nodes_to_delete);
			}
			else if(nodes_to_delete)
			{
				chain_pending_nodes(nodes_to_delete);
			}
			detete old_head;
		}else{
			chain_pending_node(old_head);
			--thread_in_pop;		
		}
	}
	void chain_pending_nodes(node* nodes)
	{
		node* last = nodes;
		while(node* const next = last->next)
		{
			last = next;
		}
		chain_pending_nodes(nodes,last);
	}
	void chain_pending_nodes(node* first,node* last)
	{
		last->next = to_be_deleted;
		while(!to_be_deleted.compare_exchange_weak(last->next,first);
	}
	
	void chain_pending_node(node *n)
	{
		chain_pending_nodes(n,n);
	}
public:
	std::shared_ptr<T>pop()
	{
		++threads_in_pop;
		node* old_head = head.load();
		//swap head to head->next 
		while(old_head && !head.compare_exchange_weak(old_head,old_head->next));
		std::shared_ptr<T>res;
		//res = old_head to pop
		if(old_head)
		{
			res.swap(old_head->data);
		}
		//the original res address should be reclaimed
		try_reclaim(old_head);
		return res;
	}
};
```
Assume for a moment that threads_in_pop is 1. You now need to try to reclaim
the pending nodes; if you don¡¯t, they¡¯ll stay pending until you destroy the stack. To do
this, you first claim the list for yourself with an atomic exchange operation c and
then decrement the count of threads_in_pop d. If the count is zero after the decrement,
you know that no other thread can be accessing this list of pending nodes.
There may be new pending nodes, but you¡¯re not bothered about them for now, as
long as it¡¯s safe to reclaim your list. You can then just call delete_nodes to iterate
down the list and delete them e.
If the count is not zero after the decrement, it¡¯s not safe to reclaim the nodes, so if
there are any f, you must chain them back onto the list of nodes pending deletion g.
This can happen if there are multiple threads accessing the data structure concurrently.
Other threads might have called pop() in between the first test of threads_in_
pop B and the ¡°claiming¡± of the list c, potentially adding new nodes to the list that
are still being accessed by one or more of those other threads. In figure 7.1, thread C
adds node Y to the to_be_deleted list, even though thread B is still referencing it as
old_head, and will thus try and read its next pointer. Thread A can¡¯t therefore delete
the nodes without potentially causing undefined behavior for thread B.
To chain the nodes that are pending deletion onto the pending list, you reuse the
next pointer from the nodes to link them together. In the case of relinking an existing
chain back onto the list, you traverse the chain to find the end j, replace the next
pointer from the last node with the current to_be_deleted pointer 1), and store the first
node in the chain as the new to_be_deleted pointer 1!. You have to use compare_
exchange_weak in a loop here in order to ensure that you don¡¯t leak any nodes that have
been added by another thread. This has the benefit of updating the next pointer from
the end of the chain if it has been changed. Adding a single node onto the list is a special
case where the first node in the chain to be added is the same as the last one 1@.
This works reasonably well in low-load situations, where there are suitable quiescent
points at which no threads are in pop(). However, this is potentially a transient situation,
which is why you need to test that the threads_in_pop count decrements to zero d
before doing the reclaim and why this test occurs before you delete the just-removed
node h. Deleting a node is potentially a time-consuming operation, and you want the
window in which other threads can modify the list to be as small as possible. The longer
the time between when the thread first finds threads_in_pop to be equal to 1 and
the attempt to delete the nodes, the more chance there is that another thread has
called pop(), and that threads_in_pop is no longer equal to 1, thus preventing the
nodes from actually being deleted.

The above is from concurrency C++, it is complicated and needing time to digest. 