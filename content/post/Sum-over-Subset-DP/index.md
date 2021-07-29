---
title: "Sum Over Subset DP"
date: 2021-07-29T12:23:59+06:00
draft: false
image: "pexels-fiona-art-5022849.jpg"
---

The best way to learn about SOS DP would probably be [This blog](https://codeforces.com/blog/entry/45223) on codeforces. But I always tend to forget how the recurrence relation is constructed and have to revisit this blog over and over again.

If you&#39;ve now understood how this whole SOS DP thing works, we can dive into some problems.

## **Codeforces 165E- Compatible**  **Numbers**

**Problem Statement** : Two numbers are &quot;compatible&quot; if the result of their bitwise AND equals zero. You are given an array of ${n}$ integers. Your task is to find the following for each array element: is this element compatible with some other element from the given array? If the answer to this question is positive, then you also should find any suitable element.

**Problem link** : [Problem - E - Codeforces](https://codeforces.com/contest/165/problem/E)

**Solution** : Suppose we&#39;re working with a number ${x}$ from the array. A number ${z}$ will be compatible with ${x}$ if for every ${1}$ bit in ${x}$, there is a ${0}$ bit in ${z}$.

Let&#39;s invert all bits of ${x}$ and call it ${y}$. We&#39;ve now made sure that ${y}$ has no common ${1}$ bit with ${x}$. Clearly, ${y}$ is compatible with ${x}$ . Now, even if we turn some ${1}$ bit of ${y}$ into ${0}$, the number is still compatible with ${x}$. Because we&#39;re not adding any new ${1}$s in ${y}$. So, any subset of ${y}$ is compatible with ${x}$. Suppose we can count the number of subsets of ${y}$ that occur in the given array. If that number is non-zero, then our answer is yes; otherwise no.

Let,

$dp[y][i]= \text{Number of occurrences of the subsets of y,} $
$\text{if we&#39;re only allowed to change the first i bits of y}$

Now let&#39;s construct the recurrence. If the ith bit of ${y}$ is ${0}$, then we can&#39;t change it ( we can&#39;t add any new 1s, remember? ). If the ith bit is ${1}$, we can either keep it that way, or we can change it to ${0}$.

So when the ith bit is ${0}$,

$dp[y][i]= dp[y][i-1]$

Otherwise,

$dp[y][i]= dp[y][i-1]+dp[y \oplus 2^i][i-1]$ ( Either keep the ${1}$, or remove it )

Unfortunately, we can only count the number of occurrences using this dp definition. But we also need to output a compatible number for each array element.

Let&#39;s redefine the dp definition now,

$dp[y][i]= \text{Any array element that is a subset of y,}$
$ \text{if we&#39;re allowed to change only the first i bits} $

The redefined recurrence relation will be,

If the ith bit is 0,

$dp[y][i]= dp[y][i-1]$

Otherwise,

$dp[y][i]= dp[y][i-1]? dp[y][i-1]:dp[y \oplus 2^i ][i-1]$

What should be the base case? If we're not allowed to change any bits, then

if $y$ exists in the array,
$dp[y][0]= y$

else,

$dp[y][0]= 0$

Here&#39;s my code:
```C++
#include <bits/stdc++.h>
#define fast ios_base::sync_with_stdio(0);cin.tie(0);
using namespace std;

int dp[2][(1<<22)+7];

int main()
{
	fast;

	int n;
	cin>>n;

	int arr[n+5];

	for( int i=0;i<n;i++ )
	{
		cin>>arr[i];

		dp[0][arr[i]]= arr[i];
	}	

	int f= 1;

	for( int i=1;i<=22;i++ )
	{
		for( int y=0;y<(1<<22);y++ )
		{
			if( y&(1<<(i-1)) )
				dp[f][y]= dp[f^1][y]?dp[f^1][y]:dp[f^1][y^(1<<(i-1))];
			else
				dp[f][y]= dp[f^1][y];
		}

		f^= 1;
	}

	for( int i=0;i<n;i++ )
	{
		int y= 0;

		for( int j=0;j<22;j++ )
			if( !(arr[i]&(1<<j)) )
				y+= 1<<j;

		if(!dp[0][y])
			dp[0][y]= -1;

		cout<<dp[0][y]<<" ";
	}
}
```