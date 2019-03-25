---
title: LeetCode Contest 43
date: 2017-08-06 14:19:22
tags: 
  - LeetCode
  - 算法 
categories: 算法
---

记录一下contest 43做题的想法。

### 650. 2 Keys Keyboard

Initially on a notepad only one character 'A' is present. You can perform two operations on this notepad for each step:

  1. `Copy All`: You can copy all the characters present on the notepad (partial copy is not allowed).
    
  2. `Paste`: You can paste the characters which are copied last time.
    
Given a number n. You have to get exactly n 'A' on the notepad by performing the minimum number of steps permitted. Output the minimum number of steps to get n 'A'.

每一步只能复制全部，或者粘贴之前复制的全部的A；

f(n):对于n，最简单的想法就是复制1个'A'n次，此时需要n步；那当n能整除2，复制n/2次2个'A'肯定比之前要快...

考虑找到n的最大约数q，那么复制n/q次即可，继而就是求f(q);

```javascript
//递归方式：
var minSteps = function(n) {
    if(n===1)return 0;

    //找最大约数，最大约数必然<=n/2
    let q = ~~(n/2);
    while(q > 0){
        if(n%q === 0)break;
        q--;
    }
    return n/q + minSteps(q);
};

//非递归
var minSteps = function(n) {
    let dp = [0,0]; //dp[i]表示i个A所需要的最小操作数
    for(let i=2; i <= n; i++){
        let q = ~~(i/2);
        while(q > 0){
            if(i%q === 0)break;
            q--;
        }
        dp[i] = i/q + dp[q]
    }
    return dp[n]
};
```

### 651. 4 Keys Keyboard

Imagine you have a special keyboard with the following keys:

Key 1: (A): Prints one 'A' on screen.

Key 2: (Ctrl-A): Select the whole screen.

Key 3: (Ctrl-C): Copy selection to buffer.

Key 4: (Ctrl-V): Print buffer on screen appending it after what has already been printed.

Now, you can only press the keyboard for N times (with the above four keys), find out the maximum numbers of 'A' you can print on screen.

Example 1:
```
Input: N = 3
Output: 3
Explanation: 
We can at most get 3 A's on screen by pressing following key sequence:
A, A, A
```

Example 2:
```
Input: N = 7
Output: 9
Explanation: 
We can at most get 9 A's on screen by pressing following key sequence:
A, A, A, Ctrl A, Ctrl C, Ctrl V, Ctrl V
```

动态规划，将dp[i]作为i个操作能得到的最多A的数量。

dp[n]，n>3时，可以是 Ctrl A, Ctrl C, Ctrl V得到的，那么这个Ctrl A从哪开始呢？

如果是从n-3(复制操作需要3步,n-3是能开始复制的最后)开始复制,那只能复制一次,即2*dp[n-3],或者更早开始，依次为3*dp[n-4],4*dp[n-5],直至(n-2)*dp[1],(有没有更早的终止条件？)
取之中最大的值，再和直接输入n次A即n比较，较大者即是dp[n]。

AC:
```js
/**
 * @param {number} N
 * @return {number}
 */
var maxA = function(N) {
    let dp = [0,1,2,3];
    for(let i = 4;i<=N;i++){
        let j = i-3,c=2;
        while(j>=1){
            if(dp[i] === void 0)dp[i]=dp[j]*c;
            else dp[i] = Math.max(dp[i],dp[j]*c);//从第几个开始乘
            c++;
            j--;
        }
        dp[i] = Math.max(i,dp[i]);
    }
    return  dp[N];
};
```

### 652. Find Duplicate Subtrees

Given a binary tree, return all duplicate subtrees. For each kind of duplicate subtrees, you only need to return the root node of any one of them.

Two trees are duplicate if they have the same structure with same node values.

Example 1: 

```
        1
       / \
      2   3
     /   / \
    4   2   4
       /
      4
```

The following are two duplicate subtrees:

```
      2
     /
    4
```
and
```
    4
```

暴力法(127/165 TLE...)
```js
var findDuplicateSubtrees = function(root) {
    if( !root )return [];
    let tree = [root], n = 0;
    while(n < tree.length){
        let tmp = tree[n++];
        tmp.left && tree.push(tmp.left);
        tmp.right && tree.push(tmp.right);
    }
    tree.sort( (a,b) => {return a.val-b.val} );

    let i = 0,res=[]; 
    while( i < tree.length-1 ){
        if( tree[i].val === tree[i+1].val ){
            let j = i+1;
            while( j<tree.length && tree[i].val === tree[j].val ){
                if( compare(tree[i],tree[j]) ){
                    let k = res.length-1,isDup = false;
                    while( k >= 0 && res[k].val === tree[j].val ){
                        if( compare(tree[j],res[k]) ){
                            isDup = true;
                            break;
                        }
                        k--;
                    }
                    if(!isDup)res.push(tree[j]);  
                }
                j++;
            }
        }
        i++;
    }
    return res;
};

function compare(a,b){
    if(a===null && b===null)return true;
    if(a===null || b===null)return false;
    if(a.val !== b.val)return false;
    return compare(a.left,b.left) && compare(a.right,b.right) 
}
```