title: 基础算法训练 - 排序专题
tags: [排序,基础算法]
categories: 算法分析
date: 2015-07-12 10:13:00
---

# 前言

以前的算法和数据结构基础不是很扎实，从现在开始，弄懂每个算法的原理，打好基础。

## 1.快速排序

快排的核心思想是分治，核心算法则是对数组的划分：

`quicksort(A,p,r):

if p < r

q = partition(A,p,r)

quicksort(A,p,q-1)

quicksort(A,q+1,r)`

其中划分函数partition的核心操作是：

1.  将数组最后一个元素A[r]作为比较对象
2.  i = p-1 , j = p, j不停自加，判断A[r]和A[j]大小，若A[j]小于A[r]则将后游标i前进1位，并交换a[i]和a[j]
3.  最后交换a[i+1]和a[r]
4.  交换所得目的数将所有比a[r]小的数放在数组前面，所有比a[r]大的数放在数组后面，a[r]则在数组中间，返回数字为比a[r]小的数的个数

代码：

`#include <iostream>

using namespace std;

int a[100] = {13,19,9,5,12,8,7,4,21,2,6,11};

void swap(int &x,int &y){

  int t = x;

  x = y;

  y = t;

}

int partition(int p,int r){

  int i = p - 1;

  int j = p;

  for(;j < r; j++){

    if(a[j] < a[r]){

      i++;

      swap(a[i],a[j]);

    }

  }

  swap(a[r],a[i+1]);

  return i+1;

}

void quicksort(int p,int r){

  if(p < r){

    int q = partition(p,r);

    quicksort(p,q-1);

    quicksort(q+1,r);

  }

}

int main(){

 quicksort(0,11); 

  // int n = partition(0,11);

  for(int i = 0 ; i < 12;i++)

    cout<<a[i]<<" ";

  cout<<endl;

}`