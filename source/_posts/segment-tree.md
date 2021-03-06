title: 数据结构总结 - 线段树专题
tags: [线段树,数据结构]
categories: 算法分析
date: 2015-07-26 22:17:00
---

## 概念

线段数是一种特殊的数据结构，它是一颗`完全二叉树`，它的每个节点保存一条线段，主要用来做高效的动态查询。线段树的区间查询复杂度为`o(logn)`,更新复杂度也为`o(logn)`

## 操作

1.  构造 `void build(int l,int r, int rt)`

    构造线段树的过程是一个递归的过程，不停二分给定的区间`[l,r]`，若`l=r`时，则直接赋值。`rt`表示当前要构建的节点，而`l`和`r`为递归区间，每次以`m=(l+r)/2`为二分点，构建左子树和右子树，然后将左子树和右子树的信息向上更新(`pushup`)。这里及以下代码以求区间和的线段树为例。

    void build(int l,int r, int rt){
        add[rt] = 0;
        if(l == r){
            return;
        }
        int m = (l + r) >> 1;
        build(lson);
        build(rson);
        pushup(rt);
    }
    `</pre>

1.  查询`query(int L,int R,int l, int r, int rt)`
    线段树查询也运用了分治的思想，不断二分起始区间，直到和查询出现交集，再回溯并合并到结果中，若有延迟更新标记，则进行更新再进行回溯。查询区间`[L,R]`和二分区间`[l,r]`满足关系有以下几种（m为二分区间中点）：

*   `L <= l <= r <= R`，即二分区间在查询区间以内，则直接回溯`[l,r]`合并到查询结果中
*   `L <= m`,则继续二分左儿子，直至`m < L`，并把属于区间内的结果回溯到最后的结果
*   `m <= R`，则继续二分右儿子，直至`m > R`，并把结果回溯刀最后结果
    <pre>`LL query(int L,int R,int l, int r, int rt){
        if(L <= l && r <=R){
            return sum[rt];
        }
        pushdown(rt, r - l + 1);
        int m = (l + r) >> 1;
        LL ret = 0;
        if(L <= m) ret += query(L, R, lson);
        if(m < R) ret += query(L, R, rson);
        return ret;
    }
    `</pre>

1.  更新

*   点更新`void updatep(int p, int add, int l, int r, int rt)`
    点更新的操作比较无闹，不断二分区间`[l,r]`，直至`l == r`，就相当于在区间`[l,r]`中二分查找p，然后将其加上需要加的数即可。
    <pre>`void updatep(int p, int add, int l, int r, int rt)  
    {  
        if( l == r )  
        {  
            sum[rt] += add;  
            return ;  
        }  
        int m = ( l + r ) >> 1;  
        if(p <= m)  
            updatep(p, add, lson);  
        else  
            updatep(p, add, rson);  

        pushup(rt);  
    }  
    `</pre>

*   区间更新`void update(int L,int R,int c,int l,int r,int rt)`
    区间更新的二分过程和区间查询的过程相同，二分出现出现区间查询中的情况一则进行更新，但是需要注意的是，为了降低区间更新复杂度，需要使用延迟标记，等到查询时再向下更新到底。因此对于情况一只进行标记更新和当前节点更新，同时做一次向下更新。对于情况二和情况三，分别更新左右子树，并向上做一次更新。
    <pre>`void update(int L,int R,int c,int l,int r,int rt){
        if(L <= l && r <=R){
            add[rt] += c;
            sum[rt] += (LL)c * (r - l + 1);
            return;
        }
        pushdown(rt,r - l + 1);
        int m = (l+r) >> 1;
        if(L <= m) update(L, R, c, lson);
        if(m < R) update(L, R, c, rson);
        pushup(rt);
    }

## 样例

### 线段树普遍题型有区间求和及区间最值（RMQ），之后会慢慢在此博客进行持续更新。