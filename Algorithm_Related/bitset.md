bitset is useful datastructure. It has functions: flip(),count(),reset().
```
//initialize
std::bitset<4>b;

//flip
std::cout<<b<<'\n'; // 0000
b.flip();//1111
b.flip(1);//1101

//all any none
   std::bitset<4> b1("0000");// all0 any 0 none 1
    std::bitset<4> b2("0101");// all 0 any 1 none 0
    std::bitset<4> b3("1111");// all 1 any 1 none 0
	
//reset: reset all elements to 0 or specific value to 0

//set the same as above

 
