title: 蓝桥杯校内选拔赛第6题题解 poj 2676
tags: [poj,dfs,蓝桥杯,搜索]
categories: 算法题解
date: 2015-02-03 18:51:54
---

题目就是输入一个9*9的数独，然后解出数独的最终解。

大体思路还是搜索，dfs即可，写一个检查函数判断行、列、小方阵是否违规。

dfs的函数变量一开始考虑用x,y坐标进行处理，然后对于每个点，枚举1-9个数字中可用的数字，但是由于每次到一行的末尾需要换行到下一行比较麻烦，索性将二维数组一维化，仅使用一个变量k即可。

<!--more-->

调试的时候出现各种错误，还是要细心的问题= =

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
int m[9][9];
char buffer[9]; 
bool check(int k,int v) {
	int x = (k-1)/9;
	int y = k - x*9 -1;
	for(int i = 0 ;i < 9; i++)  //column
		if(m[i][y] == v) return 0;
	for(int j = 0 ;j < 9; j++)  //row
		if(m[x][j] == v) return 0;
	int a,b;   //small square
	a = x/3*3;
	b = y/3*3;
	for(int i = a; i < a+3; i++)
		for(int j = b; j < b+3 ; j++) {
			if(m[i][j] == v)
				return 0;
		}
	return 1;
}

bool dfs(int k) {
	//cout<<k<<endl;
	if(k > 81) {
		for(int i = 0; i < 9; i++) {
			for(int j = 0 ; j < 9; j++)
				cout<<m[i][j]<<" ";
			cout<<endl;
		}		
		return 1;
	}

	int x = (k-1)/9;
	int y = k - x*9 -1;
	if(m[x][y] != 0) {
		dfs(k+1);
	} else {
		int i;
		for(int i = 1; i <= 9; i++) {
			if(check(k,i)) {
			//	cout<<k<<" "<<i<<endl;
				m[x][y] = i;
				if(dfs(k+1)) return 1;
				m[x][y] = 0;
			}
		}	
	}

	return 0;
}

int main() {
	freopen("in.txt","r",stdin);
	int tmp;
	int cnt = 0;

	memset(c,0,sizeof(c));
	memset(r,0,sizeof(r));
	memset(m,0,sizeof(m));
	for(int i = 0 ; i <9; i++) {
		cin>>buffer;
		for(int j = 0 ; j < 9;j++) {
			tmp = buffer[j] - '0';
			m[i][j] = tmp;

			if(tmp) {
				c[i][tmp-1]++;
				r[j][tmp-1]++;
				cnt++;				
			}				
		}
	} 
	dfs(1);
}
```

测试：

```
005300000

800000020

070010500

400005300

010070006

003200080

060500009

004000030

000009700

145327698

839654127

672918543

496185372

218473956

753296481

367542819

984761235

521839764
```