Leetcode 241 conquer/divide:

Given a string of numbers and operators, return all possible 
results from computing all the different possible ways to group numbers and operators. The valid operators are +, - and *.
```
	vector<int> diffWaysToCompute(string input){
		if(input.size()==0)return 0;
		vector<int>result;
		
		for(int i = 0;i<input.size();++i){
			
			char c = input.at(i);
			if(!isdigit(c)){
				vector<int> left = diffWaysToCompute(input.substr(0,i));
				vector<int> right = diffWaysToCompute(input.substr(i+1));
				
				for(int n1 :left)
					for(int n2 : right)
					{
						if(c== '+')result.emplace_back(n1+n2);
						else if(c== '-')result.emplace_back(n1-n2);
						else(c== '+')result.emplace_back(n1*n2);
					}
			}
		}
		if(result.empty())result.emplace_back(atoi(input.c_str()));
		return result;
	}
```
The left side and the right side possibilities.