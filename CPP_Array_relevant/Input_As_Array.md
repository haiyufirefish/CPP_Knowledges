```
#include<iostream>
using namespace std;

template <size_t size_x, size_t size_y>
void func(double(&arr)[size_x][size_y])
{
	cout<< arr[1][4] <<'\n';
}

int main()
{
	double a1[10][10] = {0};
	double a2[5][5] = {{1,2,3,4,5},{4,3,2,1,6}};

	printf("%p\n%p\n\n", &a1, &a2);
	func(a1);
	func(a2);

	return 0;
}
```
The size_t N template parameter is a deduced integral value based upon the array size passed to the template function. 
Templates parameters can be:
a non-type template parameter;
a type template parameter;
a template template parameter.
