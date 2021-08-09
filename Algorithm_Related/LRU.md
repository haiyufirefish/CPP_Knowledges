This article is about LRU.
**put" and **get"" method complexity is O(1)://
1.Obviously, the elements in the cache must have a time sequence to distinguish the most recently used and long-unused data. 
When the capacity is full, the element that has not been used the longest is deleted to make room.//
2. We need to quickly find whether a key already exists in the cache and get the corresponding val.
3. Every time you access a key in the cache, you need to change this element to the most recently used element, 
which means that the cache must support quick insertion and deletion of elements at any position.//
So **LinkedHashmap** support above functionality.
The structure of linkedHashmap satisfy this requirements:
![LinkedHashmap](./images/LinkedHashmap.jpg)
Relying on this structure, it has following charateristics:
1. If we add elements from the end of the linked list by default every time, then obviously the elements 
closer to the end are the most recently used, and the elements closer to the head are the least used.//
2. For a certain key, we can quickly locate the node in the linked list through the hash table to obtain the corresponding value//
3. The linked list obviously supports quick insertion and deletion at any position, just change the pointer. 
It's just that the traditional linked list cannot quickly access the elements at a certain position according to the index. 
With the help of the hash table, you can quickly map to any linked list node through the key, and then insert and delete it.

```
class LRUCache {
public:
    
    struct node
    {
        int val;
        int key;
        node* pre;
        node* next;
        node(){}
        node(int key,int val):key(key),val(val),pre(NULL),next(NULL){}       
    };
    
    unordered_map<int,node*>mp;
    int size;
    node* head, *tail;
    
    LRUCache(int size) {
        this->size = size;
        head = new node();
        tail = new node();
        head->next = tail;
        tail->pre = head;
    }
    
    void move_node_to_head(node* cur)
    {
        node* next = head->next;
        next->pre = cur;
        head->next = cur;
        cur->pre = head;
        cur->next = next;
    }
    
    node* delete_current_node(node* cur)
    {
        cur->pre->next = cur->next;
        cur->next->pre = cur->pre;
        return cur;
    }
    
    void make_recently(node* cur)
    {
        node* temp = delete_current_node(cur);
        move_node_to_head(temp);
    }
    
    int get(int key) {
        int res = -1;
        if(mp.count(key))
        {
            node* temp = mp[key];
            make_recently(temp);
            res = temp->val;
        }
        return res;
    }
    
    void put(int key, int value) {
        if(mp.count(key))
        {
            node* temp = mp[key];
            temp->val = value;
            make_recently(temp);
        }else
        {   
            node* cur = new node(key,value);
            if(mp.size()==size)
            {
                node* temp = delete_current_node(tail->pre);
                mp.erase(temp->key);
            }
            move_node_to_head(cur);
            mp[key] = cur;
           
        }
        
    }
};
```