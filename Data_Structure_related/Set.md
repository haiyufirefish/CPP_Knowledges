### This article talks about how to use set.
set is usually implemented as red-black tree, it is easy to search but 
difficult to insert or delete. The elements are sorted and have no duplicates.

```
set<int>ss;
	ss = { 1,3,5,23,5123,132 };
	auto li = lower_bound(ss.begin(), ss.end(), 24);
	//return the iterator of the element which is smaller than input value
	//23
	cout << *li << endl;
```
It is sorted according to the first element when storing pair.
```
set<pair<int, int>>s;
	s = { {1,2},{3,0},{5,1},{7,-1} };
	for (auto p: s) {
		cout << p.first << endl;
}

// judge if the input interval[begin,end) already exist.return true when insert successful.
set<pair<int,int>>memo;
memo = {{1,4},{7,12},{25,30}};
bool input_(int begin,int end){
	if(begin<0||end<0)return false;
	auto it = memo.lower_bound({begin,end});
	if(it!=memo.end()&&it->first < e)return false;
	if(it!=memo.begin()&&(--it)->second>begin)return false; 
	memo.insert({begin,end});
	return true;
}

```
