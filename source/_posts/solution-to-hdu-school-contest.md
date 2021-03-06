title: 杭电校赛部分赛题题解
date: 2015-11-29 22:16:08
tags: [并查集,记忆化搜索,模拟]
categories: 算法题解
---
下午大家在做题的时候我把后面几个题目写了写，写一个题解和大家分享。简单题大家自己讨论下，可能有什么坑的，自己细心点就行了。这里主要写一下`1003`，`1005`，`1006`，`1007`的题解。下午没怎么认真做，代码写的乱七八糟，大家见谅。

<!-- more -->

## 1003 玩骰子

### 题意

两个人扔骰子，根据规则（有三条、对子、散牌什么的，具体看题）比较大小，大的人赢。现在主角已经扔了一个结果了，然后对方也扔了一个结果，主角有一个机会选择一个数重新扔一次，问获胜的几率有多少。

这里有个需要理解一下的地方，**因为主角很机智**，所以肯定不会选择毫无胜算的数字来重新扔（就是选择了一个数字，重新扔骰子，扔出来的数字不管是多少都不会改变输的结果），所以算概率的时候分母注意一下不是全部的可能性，而是选择的可能性。

### 分析

这题就是个大模拟。。写一个结构体把比较函数写清楚了（**注意罗列所有情况**），然后暴力枚举吧，注意如果win的数量为0，就不要加tot了，然后取所有数里面赢的概率最大的为结果。

### 代码

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
bool cmp(int x, int y){
    return x > y;
}
int vis[6];
struct node
{
    int n[3];
    int t;
    int tn;
    int tnn;
    node(int a, int b, int c){
        n[0] = a;
        n[1] = b;
        n[2] = c;
        sort(n, n+3, cmp);
        if(n[0] == n[2]){
            t = 3;
            tn = n[0];
        }else if(n[0] == n[1]){
            t = 2;
            tn = n[1];
            tnn = n[2];
        }else if(n[1] == n[2]){
            t = 2;
            tn = n[1];
            tnn = n[0];
        }else{
            t = 1;
            tn = n[0];
        }
    }
    int ncmp(node y){
        if(t == y.t){
            if(t == 1){
                if(n[0] == y.n[0]){
                    if(n[1] == y.n[1]){
                        if(n[2] == y.n[2]){
                            return 2;
                        }else{
                            return n[2] > y.n[2];
                        }
                    }else{
                        return n[1] > y.n[1];
                    }
                }else{
                    return n[0] > y.n[0];
                }                
            }else if(t==2){
                if(tn == y.tn){
                    return tnn > y.tnn;
                }else{
                    return tn > y.tn;
                }
            }else {
                return n[0] > y.n[0]; 
            }
        }else{
            return t > y.t;
        }
    }
    void print(){
        cout<<n[0]<<" "<<n[1]<<" "<<n[2]<<" "<<t<<endl;
    }
};
int main(){
    int a, b, c,d,e,f;
    int t;
    scanf("%d",&t);
    while(t--){
        scanf("%d%d%d%d%d%d",&a,&b,&c,&d,&e,&f);
        node x = node(a,b,c);
        node y = node(d,e,f);
        // x.print();
        // y.print();
        // cout<<x.ncmp(y)<<endl;        
        if(x.ncmp(y)==1){
            printf("%.3lf\n", 1.0);
            continue;
        }
        int tot = 6;
        int win = 0;
        double ans = 0;
        for(int i = 1; i <=6; i++){
            node z = node(i,b,c);
            if(z.ncmp(y)==1){
                win++;
            }
        }
        ans = max(ans,(double)win/(double)tot);

        win = 0;
        for(int i = 1; i <=6; i++){
            node z = node(a,i,c);
            if(z.ncmp(y)==1){
                win++;
            }
        }
        ans = max(ans,(double)win/(double)tot);

        win = 0;
        for(int i = 1; i <=6; i++){
            node z = node(a,b,i);
            if(z.ncmp(y)==1){
                win++;
            }
        }    
        ans = max(ans,(double)win/(double)tot);
       
        printf("%.3f\n", ans);      
    }

}
```

## ACM组队安排

### 题意

n个人组队，每队人数可以为1或2或3，问组队的方案数。

### 思路

```
void dfs(int a, int b, int c){
	//a为组队人数3的队伍数，b为组队人数2的队伍数，类推。。。
	dfs(a,b+1,c-2) //取两个孤立的人组成一队2个人的
	dfs(a+1,b-1,c-1) //取1个孤立的人和1队2人的组成1队3人的
}
```
为了把所有状态都搜遍，我们从`dfs(0,0,n)`开始，按上述的搜索方法进行搜索，标记已经访问过的状态vis[a][b][c]。

然后对于某个状态`(a,b,c)`，求方案数用排列组合的思路搞搞就出来了。

例如`(1,2,1)`，方案数是`C(3,8)/1! * (C(2,5) * C(2,3))/2! * C(1,1)/1!`。具体就是，8个人挑3个人，5个人挑2个，3个人挑2个，剩下1个人, 各算出方案数，然后乘法原理乘一下就是的了。


### 代码

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#define ll long long
using namespace std;
ll ans = 0;
ll fact[11];
int vis[25][25][25];
void init(){
    fact[1] = 1;
    for(int i = 2; i <= 10; i++){
        fact[i] = fact[i-1]*i;
    }
}
ll c2(int n){
    if(n < 2)
        return 1;
    return n*(n-1)/2;
}
ll c3(int n){
    if(n < 3)
        return 1;
    return n*(n-1)*(n-2)/6;
}

ll sum(int a, int b, int c){
    ll t = 1;
    int k = a*3+b*2+c;
    for(int i = 1; i <= a; i++){
        t *= c3(k);
        k -= 3;
    }
    if(a)
        t/=fact[a];
    for(int i = 1; i <= b; i++){
        t *= c2(k);
        k -= 2;
    }
    if(b)
        t/=fact[b];    
    return t;
}
void dfs(int a, int b, int c){
    vis[a][b][c] = true;
    // cout<<a<<" "<<b<<" "<<c<<" "<<sum(a,b,c)<<endl;
    ans += sum(a,b,c);
    if(c-2>=0 && !vis[a][b+1][c-2]){
        dfs(a, b+1, c-2);
    }
    if(b-1 >= 0 && c-1 >=0 && !vis[a+1][b-1][c-1]){
        dfs(a+1, b-1, c-1);
    }
}
int main(){
    int n;
    init();
    while(~scanf("%d",&n) && n){
        ans = 0;
        memset(vis,0,sizeof(vis));
        dfs(0,0,n);
        cout<<ans<<endl;
        // cout<<sum(1, 1, 0)<<endl;
    }
}
```

**以上思路为错误方法~~~**(虽然可以过这道题)。

在写的时候忽然发现这是一道**很简单的dp**...

```
f[i]=f[i-1];//第i个人自己组队
f[i]+=f[i-2]*(i-1);//第i个人与前面i-1个人中的一个人组队，有i-1种方法，剩下的i-2个人进行组队，即f[i-2]种方法。
f[i]+=f[i-3]*(i-1)*(i-2)/2;//前面的i-1个人中，挑2个人和第i个人组队(C(2,n-1)，剩余i-3个人组队(f(i-3))
```
然后f[1]，f[2]，f[3]都告诉你了，然后你懂的了。。

所以大家还是按正确的方法写吧...ORZ~
## 1006 逆袭指数

### 题意

给定一个数n，求n的最长的连续的因子，输出为`i*(i+1)*(i+2)...`。同时乘起来的因子还是n的因子。
如`630：5*6*7`，5\*6\*7=210是630的因子。

### 分析

直接上去暴力搞，枚举n的所有因子p，然后从p开始向后枚举，枚举到最长的时候就行。由于大多数因子连续都在前面的数里，所以复杂度不会很高。

**注意：**从p向后枚举时要枚举到n为止而不是sqrt(n)。

### 代码

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <cstring>
using namespace std;
vector <int> fac;
vector <int>::iterator it;
bool vis[1000000];
int ans = 0;
vector<int> path;
long long n;

void dfs(int k){
    int len = 0;
    long long t = n;
    vector<int> v;

    for(int i = k; i <= n; i++){
        if(t % i == 0){
            t /= i;
            vis[i] = true;
            len++;
            v.push_back(i);
        }else{
            break;
        }
    }
    if(len > ans){
        ans = len;
        int size = v.size();
        path.clear();
        for(int i = 0; i <= size; i++)
            path.push_back(v[i]);
    }
}
int main(){
    while(~scanf("%lld",&n)){
        ans = 0;
        int size = fac.size();
        for(int i = 2; i*i <= n; i++){
                dfs(i);
        }
        size = path.size();
        if(ans == 0){
            puts("1");
            cout<<n<<endl;
            continue;
        }
        cout<<ans<<endl;
        for(int i = 0; i < ans; i++){
            if(i < ans-1)
                cout<<path[i]<<"*";
            else
                cout<<path[i];
        }
        cout<<endl;
    }
}
```

## 1007 油菜花王国

### 题意

现在有n个精力，输入他们的能力k[i]，如果k[i]是斐波那契数，那么他的威望值f[i]为1，反之为0.然后给了m个关系(u,v)表示u和v是同一个家族，家族的威望是他们的和。最后求威望最大的家族的威望。

### 分析

额，非常裸的**并查集**。

首先，斐波那契判断很方便，预处理一下存数组就行了。**注意：不用快速幂什么的。。因为大概第45项就超过1000000000了。。**，然后判断也就从前往后扫如果相等就返回。。反之随便搞搞。。

然后是并查集，直接上模板，记得合并`merge`操作的时候，把子节点的威望加到父节点上去。

### 代码

```cpp
#include <iostream>
#include <cstring>
#include <cstdio>
using namespace std;
#define ll long long
ll fibo[50];
int f[1050];
int fa[1050];
void init(){
    fibo[1] = 1;
    fibo[2] = 1;
    for(int i = 3; i <= 45; i++){
        fibo[i] = fibo[i-2] + fibo[i-1];
    }
}
int check(int n){
    for(int i = 1; i <= 45; i++){
        if(n == fibo[i])
            return 1;
        if(n < fibo[i])
            return 0;
    }
}
int find(int x)
{
    if(x != fa[x])
        fa[x] = find(fa[x]);
    return fa[x];
}
void merge(int x,int y)
{
    int a = find(x),b = find(y);
    if(a != b){
        fa[b] = a;
        f[a] += f[b];
    }
}
int main(){
    init();
    int n,m;
    while(~scanf("%d%d",&n,&m)){
        int u,v,t;
        memset(f,0,sizeof(f));
        memset(fa,0,sizeof(fa));
        for(int i = 1; i <=n; i++){
            fa[i] = i;
            scanf("%d",&t);
            f[i] = check(t);
        }
        for(int i = 0; i < m; i++){
            scanf("%d%d",&u,&v);
            merge(u,v);            
        }
        int ans = 0;
        for(int i = 1; i <= 1000; i++){
            if(f[i] > ans){
                ans = f[i];
            }
        }
        printf("%d\n", ans);
    }
}
```

### 最后说两句

题目都很简单，大家最好在之后都去把没做出来的题目再做做，继续刷题！

