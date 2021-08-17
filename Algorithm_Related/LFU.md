Least frequently used cache is a typical cache. 
Method 1:
Store the node using binary search tree. 
```
class LFUCache {
private:
    struct CacheNode
    {
        int key;
        int value;
        int freq;
        int tick;
    
        bool operator <(const CacheNode &rhs) const
        {
            if(freq<rhs.freq)return true;
            if(freq==rhs.freq)return tick<rhs.tick;
            return false;
        }
    };
     set<CacheNode> cache_;
    unordered_map<int,CacheNode>mp;
    int tick_;
    int capacity_;
    
    void touch(CacheNode& node)
    {
        cache_.erase(node);
        ++node.freq;
        node.tick = ++tick_;
        cache_.insert(node);
    }
public:
   
    LFUCache(int capacity):capacity_(capacity),tick_(0) {
        
    }
    
    int get(int key) {
        auto it = mp.find(key);
        if(it == mp.cend())return -1;
        touch(it->second);
        int value = it->second.value;
        return value;
    }
    
    void put(int key, int value) {
        if(capacity_==0)return;
        
        auto it = mp.find(key);
        
        if(it!=mp.cend())
        {
            it->second.value = value;
            touch(it->second);
            return;
        }
        
        if(mp.size()==capacity_)
        {
            const CacheNode& node = *cache_.cbegin();
            mp.erase(node.key);
            cache_.erase(node);
        }
        
        CacheNode node{key,value,1,++tick_};
        mp[node.key] = node;
        cache_.insert(node);
    }
};
```