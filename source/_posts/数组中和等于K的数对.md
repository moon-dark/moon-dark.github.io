---
title: 数组中和等于K的数对
date: 2018-01-11 17:16:31
tags:
  - code
  - algorithm
---
[51node](http://www.51nod.com/)上面的算法题：数组中和等于K的数对
```
给出一个整数K和一个无序数组A，A的元素为N个互不相同的整数，找出数组A中所有和等于K的数对。
例如K = 8，数组A：{-1,6,5,3,4,2,9,0,8}，所有和等于8的数对包括(-1,9)，(0,8)，(2,6)，(3,5)。
```
最开始使用循环算法，导致数据量大的时候超时；后来反应过来使用``二分查找法``
<!-- more -->
``` C++
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;

int binarySearch(int *array, int lowerbound, int upperbound, int key)
{
   int position;

   // To start, find the subscript of the middle position.
   position = ( lowerbound + upperbound) / 2;

   while((array[position] != key) && (lowerbound <= upperbound))
   {
     if (array[position] > key)               // If the number is > key, ..
     {                                                       // decrease position by one.
        upperbound = position - 1;    
     }                                                      
     else                                                
     {                                                        // Else, increase position by one.
        lowerbound = position + 1;     
     }
     position = (lowerbound + upperbound) / 2;
   }

   return lowerbound <= upperbound ? position : 0;  // you may also consider returning the subscript
}

static int MAX_N = 50000;
string solveMeFirst(int K, int* A, int N) {
   int p1=A[0], p2=N;
   string res = "";
   char buf[50];
   if(A[0] > K || A[N-1] < K)
       return "No Solution";

   for(int i=0;i<N;i++) {
       int pos = binarySearch(A, i, p2, K-A[i]);
       if(pos > 0) {
           p2 = pos;
           if(i == pos) break;
           if(A[i] > A[pos]) return res;
           sprintf(buf,"%d %d\n",A[i], A[pos]);
           res.append(buf);
       }
   }

   if(res.compare("") == 0) {
       res = "No Solution";
   }

   return res;
}

int main() {
  int K, N;
  scanf("%d %d",&K,&N);
  int* A = new int[MAX_N];
  for(int i=0; i<N;i++)
      scanf("%d",&A[i]);
  sort(A, A+N);

  printf("%s",solveMeFirst(K, A, N).c_str());
  return 0;
}

```

[导致超时的输入值：==>](/assets/51nod_Problem_1001_Test_19_In.txt)
