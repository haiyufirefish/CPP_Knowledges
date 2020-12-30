Typical bitwise operations: * | ^ >> << &.
^: Using most frequently in cryptography. A^B^A = B.
<<: 2 to power of n : 1<< n.
A&~A:=0
Other operators are easy to use.

Typical problems: count the occurences of elements in containers. 
Is it a variant of DP problem? A counter must be designed to recode the state.

For simplicity, a O(n^2) method is introduced:
First of all, tranform all elements into binary form. If an element occurs n times,
the number of 1s in its binary form also appears n times.
```
int vec = {1,2,3,1,2,3,4};
/*
*	01,10,11,01,10,11,100
*	1 in first and second place occurs 2 times, in third place occurs 1 times.
*	2%2 =0,1%2 =1 
*
*/

int ans = 0;
        for(int i = 0;i<32;i++){
            int count = 0;           
            for(int num :nums){
                if ((num >> i)&1){
                    count += 1;
                }                   
            }            
            ans |= (count % 2) << i;
        }
                   
        return ans;
```
Therefore, it is only necessary to require the remainder of 
dividing all the elements in the count array by n to realize the mutual cancellation of the repeated numbers for n times.
But this method costs too much time and extra space if the elements are not very big.
In leetcode 137 discussion, it offers a binary operation method to solve this type problems optimally:
```
Assume a m bit counter: C1,C2,...,Cm. When it hits 1, it increases 1, otherwise remains 0.
```
C1 = i ^ C1;
C2 = C2 ^ (C1&i);
Cm = Cm ^ (Cm-1&Cm-2&....&C1&i);
//This is similiar with binary operator add
//If counter equals K times, it should be set to zero
// K = kj kj-1 kj-2 ....k2 k1
// Cm & mask = 1 only if Cm != K
mask = ~(Sj&Sj-1&...&S1)
// Sj = Cj if kj =0
C1 &= mask;
...
Cm &= mask;
```
Which value should be returned, the easiest way is to return C1|C2|C3|C4|.....|Cm. 
Assume this only occurs p times elements:  p = pm..p1, if p1 = p2 = 1, this time p = 3, C1 and C2 are ok for returning.
Which place equals 1, whose reminder of counter stays 1.
   