This article is about KMP algorithm, which is the classic al for DP programming.
This is the summary of two articles: * [jakeboxer](http://jakeboxer.com/blog/2009/12/13/the-knuth-morris-pratt-algorithm-in-my-own-words/)
* [fk-al](https://github.com/labuladong/fucking-algorithm/blob/master/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E4%B9%8BKMP%E5%AD%97%E7%AC%A6%E5%8C%B9%E9%85%8D%E7%AE%97%E6%B3%95.md)
The key of KMP, is the partial match table.
The KMP algorithm never retreats the pointer i of txt, does not go back (will not scan txt repeatedly), but uses the information stored in the dp array to move pat to the correct position to continue matching, and the time complexity is only O(N) ,
Use space for time, it is a dynamic programming algorithm.
Generally solution:
```
public class KMP{
private:
	
	string pat;
	vector<vector<int>>dp(pat.size(),vector<int>(256,0));
	
public:
	KMP(string pat):pat(pat){}
	vector<int> kmpcompress(string needle)
	{	
		int n = pat.size();
		int X = 0;
		for(int j = 1;j< n; ++j)
		{
		
			for(int c = 0; c<256;++c)
			{
				if(pat[j] == c)
				{
					dp[j][c] = j+1;
				}else
				{
					dp[j][c] = dp[X][c];
				}
				
			}
			X = dp[X][pat[j]];
		}
	}
	int search(string haystack)
	{
		int m = haystack.size();
		int n = pat.size();
		if(n == 0)return 0;
		if(n > m) return -1;
		
		int j = 0;
		for(int i = 0;i < m;++i)
		{
			j = dp[j][haystack[j]];
			
			if(j == n)return i-n+1;
		}
		return -1;
	}
}
```
For space(M) solution:
```
vector<int> Kmpcompress(string needle) 
{
	vector<int> res(needle.size(), 0);
	for (int len = 0, i = 1; i < needle.size();)
	{
		if (needle[i] == needle[len])
		{
			res[i++] = ++len;
		}
		else if (len)
		{
			len = res[len - 1];
		}
		else 
		{
			res[i++] = 0;
		}
	}
	return res;
}

int KMP_Implementation::strStr(string haystack, string needle) {
	int m = haystack.size(), n = needle.size();
	if (!n) {
		return 0;
	}
	vector<int> dp = Kmpcompress(needle);
	
	for (int i = 0, j = 0; i < m;) {
		if (haystack[i] == needle[j]) {
			i++, j++;
		}
		if (j == n) {
			return i - j;
		}
		if (i < m && haystack[i] != needle[j]) {
			j ? j = dp[j - 1] : i++;
		}
	}
	return -1;
}
```