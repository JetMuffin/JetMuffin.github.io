title: hdu 5088 2015多校联训第一场 1001 因数分解
tags: [多校联训,因数分解]
categories: 算法题解
date: 2015-07-21 23:49:00
---

## 题目

题目链接：[OO’s Sequence](http://acm.hdu.edu.cn/showproblem.php?pid=5288)

## 题意

题意比较明确，f(l,r)表示[l,r]区间内，满足条件（对于任意区间内的某个数i自身外的其他数j，均不存在i%j==0）的i的个数，最后则是要求所有区间（枚举所有区间）的f函数和。
<!--more-->

n范围为10^5，a<sub>i</sub>范围10^4

## 思路

一开始上手总觉得这复杂度怎么做很难达到n^2以内，之后经良哥指点得出一好方法。与其求区间内满足条件的i的个数，不如求i有贡献的区间。

对于每个i，获取其左端能整除它的最近的数的下标l,其右端能整除它的最近的数的下标r，对于区间[l+1,r-1]之内的所有包括i在内的子区间均有贡献作用，而在[1,l]和[r,n]之间因为存在数能够整除i，因此i不满足题意条件，无贡献。因此只要枚举所有的数i,累加(i - l[i] + 1) * (r[i] - i + 1)即为答案。

所以问题就变为如何获取l和r，最开始的想法是暴力向两端枚举，果断超时；然后考虑用素数的特点取剪枝枚举，结果超时+1,而且对于1的情况还不好判断；最后考虑分解因数，用数组维护每个数出现位置，对于当前数i，枚举其因数，并取其最接近于i的一个。

再交一发后发现还是会T，最后提前用vector对10^4范围的数打了一个因数表，然后进行枚举，就能过了。。ORZ

## 代码

```cpp
#include <iostream>
  #include <cstdio>
  #include <algorithm>
  #include <cstring>
  #include <map>
  #include <cmath>
  #include <vector>
  using namespace std;
  #define LL long long
  const int maxn = 100005;
  const int mod = 1000000007;
  const int N = 10001;
  int f[maxn];
  int l[maxn];
  int r[maxn];
  int vis[maxn];
  vector<int> fa[10010]; 

  int main(){
    int n;
    for(int i=1;i<=10000;i++)  
      {  
          for(int j=1;j<=i;j++)  
          {  
              if(i%j==0)  
              {  
                  fa[i].push_back(j);   
              }   
          }   
      }   
    while(~scanf("%d",&n)){
      int cnt = 0;
      memset(vis,0,sizeof(vis));
      // m.clear();
      for(int i = 1 ; i <= n; i++){
        scanf("%d",f+i);
      }
      LL ans = 0;
      for(int i = 1 ; i <= n; i++){
        l[i] = 1;
        for(int k = 0; k < fa[f[i]].size();k++ ){
          int j = fa[f[i]][k];
          if(vis[j]){
            // cout<<i<<" "<<f[i]<<" "<<j<<" "<<vis[j]<<" @"<<endl;
            int t = i - vis[j] - 1;
            if(t < i - l[i])
              l[i] = vis[j] + 1;
          }    
        }   
        vis[f[i]] = i;
      }
      memset(vis,0,sizeof(vis));
      for(int i = n ; i > 0; i--){
        r[i] = n;
        for(int k = 0; k < fa[f[i]].size();k++ ){
          int j = fa[f[i]][k];
          if(vis[j]){
              // cout<<f[i]<<" "<<j<<" "<<"#"<<endl;
              int t = vis[j] - i - 1;
              if(t < r[i] - i )
                r[i] = vis[j] - 1;
          }
        }
        vis[f[i]] = i;
      }

      // for(int i = 1 ; i <= n; i++){
      //   cout<<l[i]<<" "<<i<<" "<<r[i]<<endl;
      // }
      for(int i = 1 ; i <= n; i++){
        ans = (ans + (i - l[i]  + 1) * (r[i] - i + 1) )% mod;
      }    
      printf("%lld\n",ans);
    }
  }  
``` 