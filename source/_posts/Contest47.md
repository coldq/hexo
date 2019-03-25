---
title: LeetCode Contest 47
date: 2017-08-27 18:11:12
tags: 
  - LeetCode
  - 算法 
categories: 算法
---

记录下做题思路。

### 665.Non-decreasing Array

Given an array with `n` integers, your task is to check if it could become non-decreasing by modifying **at most** `1` element.

We define an array is non-decreasing if `array[i] <= array[i + 1]` holds for every `i` (1 <= i < n).

**Example 1:**

```
Input: [4,2,3]
Output: True
Explanation: You could modify the first 4 to 1 to get a non-decreasing array.

```

**Example 2:**

```
Input: [4,2,1]
Output: False
Explanation: You can't get a non-decreasing array by modify at most one element.
```

```javascript
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var checkPossibility = function(nums) {
    let flag = true;
    for(let i=1;i<nums.length; i++){
        if(nums[i] < nums[i-1]){
            if(!flag) return flag;
            if(i>1&& nums[i] < nums[i-2]) {// 如果当前这个数比前2个数都小，就删去，然后继续判断
                nums.splice(i,1);
                i--;
            }
            flag = false;//否则，当前数比之前小，但比i-2要大，那么在当前数上继续判断
        }
    }
    return true;
};
```



### 666. Path Sum IV

If the depth of a tree is smaller than `5`, then this tree can be represented by a list of three-digits integers.

For each integer in this list:

1. The hundreds digit represents the depth `D` of this node, `1 <= D <= 4.`
2. The tens digit represents the position `P` of this node in the level it belongs to, `1 <= P <= 8`. The position is the same as that in a full binary tree.
3. The units digit represents the value `V` of this node, `0 <= V <= 9.`

Given a list of `ascending` three-digits integers representing a binary with the depth smaller than 5. You need to return the sum of all paths from the root towards the leaves.

```javascript
var pathSum = function(nums,sum=0) { 
    if(!nums || nums.length===0) return 0;
    let root = nums[0]%100%10;
    if(nums.length === 1) return root+sum;
    let left=[],right=[];
    for(let i=1; i< nums.length; i++){、
    	//LorR：十位数，标志是左子树还是右子树
    	//level：当前在第几层
        let LorR = ~~(nums[i]%100/10), level = ~~(nums[i]/100);
                                      
        //分别放入左右子树数组，并对百位数-100，如果是右子树，十位数减去左边的个数
        if(LorR <= Math.pow(2,level-2)) left.push(nums[i]-100);
        else right.push(nums[i]-100 - 10 * Math.pow(2,level-2));
    }
    let res = 0;
    if(left.length){
        res += pathSum(left,root+sum); //递归调用，传入当前和
    }
    if(right.length){
        res += pathSum(right,root+sum);
    }
    return res;
}; 
```

### 667.Beautiful Arrangement II

Given two integers `n` and `k`, you need to construct a list which contains `n` different positive integers ranging from `1` to `n` and obeys the following requirement: 
Suppose this list is [a1, a2, a3, ... , an], then the list [|a1 - a2|, |a2 - a3|, |a3 - a4|, ... , |an-1 - an|] has exactly `k` distinct integers.

If there are multiple answers, print any of them.

**Example 1:**

```
Input: n = 3, k = 1
Output: [1, 2, 3]
Explanation: The [1, 2, 3] has three different positive integers ranging from 1 to 3, and the [1, 1] has exactly 1 distinct integer: 1.

```

**Example 2:**

```
Input: n = 3, k = 2
Output: [1, 3, 2]
Explanation: The [1, 3, 2] has three different positive integers ranging from 1 to 3, and the [2, 1] has exactly 2 distinct integers: 1 and 2.
```

```javascript
/**
 * @param {number} n
 * @param {number} k
 * @return {number[]}
 */
var constructArray = function(n, k) {
    let res = [];
    for(let i=0;i<n;i++) res[i] = i+1;
    if(k===1) return res;
    let i = 1, sign = true;
    //有k个不同差值，那就从1到k，对1-n中前k个数进行变换
    while(k > 0){
        if(sign) res[i] = res[i-1] + k; // 1,1+k,1+k-(k-1)...
        else res[i] = res[i-1] - k;
        k--;
        i++;
        sign = !sign;
    }
    return res;
};
```
### 668. Kth largest Number in Multiplication Table

  Nearly every one have used the [Multiplication Table](https://en.wikipedia.org/wiki/Multiplication_table). But could you find out the `k-th` largest number quickly from the multiplication table?

Given the height `m` and the length `n` of a `m * n` Multiplication Table, and a positive integer `k`, you need to return the `k-th` largest number in this table.

**Example 1:**

```
Input: m = 3, n = 3, k = 5
Output: 
Explanation: 
The Multiplication Table:
1	2	3
2	4	6
3	6	9

The 5-th largest number is 3 (1, 2, 2, 3, 3).

```

**Example 2:**

```
Input: m = 2, n = 3, k = 6
Output: 
Explanation: 
The Multiplication Table:
1	2	3
2	4	6

The 6-th largest number is 6 (1, 2, 2, 3, 4, 6).
```

```javascript
/**
 * @param {number} m
 * @param {number} n
 * @param {number} k
 * @return {number}
 */
var findKthNumber = function(m, n, k) {
   let low = 1, high = m * n;
    while (low != high) {
        let mid =~~((low + high) / 2);
        let cnt = 0;
        for (let i = 1; i <= m; ++i) {
            cnt += Math.min(~~(mid / i), n);
        }
        if (cnt < k) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }
    return high;
};
```