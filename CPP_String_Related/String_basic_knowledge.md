### string to integer
```
string s = "45";
int n = 0;
for(int i = 0;i<s.size();i++)
{
	char c = s[i];
	n *=10 + (c - '0');
}
```
### dealing with basic add and minus
```
int calculate(string s){
	stack<int> st;
	int num =0;
	char sign = '+';
	
	for(int i =0;i<s.size();i++)
	{
		char c = s[i];
		if(isdigit(c))
			num*=10+(c-'0');
		
		if(!isdigit(c)||i==s.size()-1)
		{
			switch(sign)
			{
				case '+':
					stack.push(num);break;
				case '-':
					stack.push(-num);break;
			}
			sign = c;
			num = 0;
		}
	}
int res =0;
while(!stack.empty())
{
	res += st.top();
	st.pop();
}
return res;
}
```
### Dealing with multiplication and division
Es is easy to deal with the both sign. The last digit is stored in the top of 
stack, multiple it or use it as divisor.And then pushing it back to stack.
```
....
	switch(sign)
	{
	case '+':
		stack.push(num);break;
	case '-':
		stack.push(-num);break;
	case '*':
	{
		int t = stack.top();
		stack.pop();
		t *= num;
		stack.push(t);
		break;
	}
	case '/':
	{
		int d = stack.top();
		stack.pop();
		d /= num;
		stack.push(d);
		break;
	}
	}
	num = 0;
	sign = c;
...
### When there are spaces
Just adding a condition to ignore them
```
	...
	if((!isdigit(c)&&c!=' ')||i==s.size()-1)
	...
```
### Handling brackets
Think about recursion.
For example: 3*(9+3/2)+10:= 3* calculate(9+3/2)+10 
The final version:
```	
int calculate_(string &s,int &index) {
	stack<int> st;
	int num = 0;
	char sign = '+';

	while(index<s.size())
	{
		char c = s[index];
		if (isdigit(c))
			num = 10*num + (c - '0');

		if (c == '(') {
			
			num = calculate_(s,++index);
		}
		if ((!isdigit(c) && c != ' ') || index == s.size() - 1)
		{
			switch (sign)
			{
			case '+':
				st.push(num); break;
			case '-':
				st.push(-num); break;
			case '*': {
				int t = st.top();
				st.pop();
				t *= num;
				st.push(t);
				break;
			}	
			case '/': {
				int d = st.top();
				st.pop();
				d /= num;
				st.push(d);
				break;
			}
				
			}
			num = 0;
			sign = c;
		}
		if (c == ')')break;
		index++;
	}
	int res = 0;
	while (!st.empty())
	{
		res += st.top();
		st.pop();
	}
	return res;
    }
```	