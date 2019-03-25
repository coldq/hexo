---
title: 各种排序汇总
date: 2017-05-29 16:22:48
tags: 
  - 算法
categories: 算法
---

<img src="http://o9qr6mev3.bkt.clouddn.com/sort.jpg" style="width: 80%;height: 100%;" alt="排序" align=center />

以下都是js的简单实现。

### 排序的稳定性

假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序，这些记录的相对次序保持不变，即在原序列中，ri=rj，且ri在rj之前，而在排序后的序列中，ri仍在rj之前，则称这种排序算法是稳定的；否则称为不稳定的。

选择排序、快速排序、希尔排序、堆排序不是稳定的排序算法；而冒泡排序、插入排序、归并排序和基数排序是稳定的排序算法。

### 1.直接插入

基本思想：前i个数是已经排好的数，然后把第i+1个数插入其中。

```js
function insertSort(arr){
    let len = arr.length;
    for(let i = 1; i < len; i++){
        for(let j = i; j > 0; j--){
            if(arr[j] < arr[j-1]) swap(arr,j,j-1);
            else break;
        }
    }
}
```

### 2.快速排序

基本思想：挑选一个元素，称为 “基准”（pivot），重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边），这个称为分区（partition）操作。然后对两个分区进行递归。

<img src="http://o9qr6mev3.bkt.clouddn.com/quick.jpg" style="width: 400px;height: 300px" alt="排序" align=center />
 
```js
function quickSort(arr){
    partition(arr,0,arr.length-1);
}
function partition(arr,lo,hi){
    if(lo >= hi) return;
    let i = lo,j = hi;
    while(i < j){
        while(j>i &&arr[j] >= arr[i])j--;
        swap(arr,i,j)
        while(j>i && arr[i] <= arr[j])i++;
        swap(arr,i,j);
    }
    partition(arr,lo,j-1);
    partition(arr,j+1,hi);
}
```

#### 2.1选择基准

##### 方法(1)：固定位置
思想：取序列的第一个或最后一个元素作为基准

##### 方法(2)：随机选取基准
引入的原因：在待排序列是部分有序时，固定选取枢轴使快排效率底下，要缓解这种情况，就引入了随机选取枢轴
具体操作：选择基准，把枢轴位置的元素和low位置元素互换，此时可以和普通的快排一样调用划分函数

##### 方法(3)：三数取中（median-of-three）
引入的原因：虽然随机选取枢轴时，减少出现不好分割的几率，但是还是最坏情况下还是O(n^2），要缓解这种情况，就引入了三数取中选取枢轴

#### 2.2优化

##### 优化1、当待排序序列的长度分割到一定大小后，使用插入排序。

原因：对于很小和部分有序的数组，快排不如插排好。当待排序序列的长度分割到一定大小后，继续分割的效率比插入排序要差，此时可以使用插排而不是快排

截止范围：待排序序列长度N = 10，虽然在5~20之间任一截止范围都有可能产生类似的结果，这种做法也避免了一些有害的退化情形。摘自《数据结构与算法分析》Mark Allen Weiness 著

##### 优化2、在一次分割结束后，可以把与Key相等的元素聚在一起，继续下次分割时，不用再对与key相等元素分割：(对有很多重复元素的数组排序提升很大)

具体过程：在处理过程中，会有两个步骤

第一步，在划分过程中，把与key相等元素放入数组的两端

第二步，划分结束后，把与key相等的元素移到枢轴周围

### 3.选择排序

思路：每次从剩余 n-i 个数中找出最小的，放到第i位上。不稳定，比如 [5,5,3] 5和3会交换，导致原本在前面的5到了后面。

<img src="http://o9qr6mev3.bkt.clouddn.com/select.jpg" style="width: 400px;height: 300px" alt="排序" align=center />


```js
function selectSort(arr){
    for(let i = 0; i < arr.length; i++){
        let min = i;
        for(let j = i+1; j < arr.length; j++){
            if(arr[j] < arr[min]) min = j;
        }
        swap(arr,min,i);
    }
}
```
#### 3.1优化

二元选择排序：简单选择排序，每趟循环只能确定一个元素排序后的定位。我们可以考虑改进为每趟循环确定两个元素（当前趟最大和最小记录）的位置,从而减少排序所需的循环次数。

### 4.归并排序

思路：每次把两个排好序的数组归并在一起。

<img src="http://o9qr6mev3.bkt.clouddn.com/merge.jpg" style="width: 400px;height: 300px" alt="排序" align=center />


```js
function mergeSort(arr){
    if(arr.length === 1) return arr;
    let mid = ~~(arr.length/2);
    let left = arr.slice(0,mid),right = arr.slice(mid);
    return merge(mergeSort(left),mergeSort(right));
}
function merge(a,b){
    let res = [],
        i = 0,
        j = 0;
    while(i < a.length && j < b.length) {
        if(a[i] > b[j]) res.push(b[j++]);
        else res.push(a[i++]);
    }
    if(i < a.length) res = [...res,...a.slice(i)];
    if(j < b.length) res = [...res,...b.slice(j)];
    return res;
}
```

### 5.希尔排序

基于插入排序的优化排序，按照不同步长对元素进行插入排序，当刚开始元素很无序的时候，步长最大，所以插入排序的元素个数很少，速度很快；当元素基本有序了，步长很小，插入排序对于有序的序列效率很高(排好序的数组，插入排序O(N)复杂度)。希尔排序是不稳定的。

<img src="http://o9qr6mev3.bkt.clouddn.com/shell.jpg" style="width: 400px;height: 300px" alt="排序" align=center />

```js
function shellSort(arr){
    let d = ~~ (arr.length/2);
    while(d>=1){
        for(let i = 0; i < d; i++){
            for(let j = i+d; j < arr.length; j += d){
                for(let k = j; k >= d; k-=d){
                    if(arr[k] < arr[k-d]) swap(arr,k,k-d);
                    else break;
                }
            }
        }
        d = ~~(d/2);
    }
}
```
### 6.冒泡排序

思路：两两比较，大的上浮（或者小的），一趟下来最大的数浮到了顶部。如果一次交换都没发生，说明排序完成。

<img src="http://o9qr6mev3.bkt.clouddn.com/bubble.jpg" style="width: 400px;height: 300px" alt="排序" align=center />

```js
function bubbleSort(arr){
    for(let i = arr.length-1; i>0; i--){
        let count = 0;
        for(let j = 0; j < i; j++){
            if(arr[j+1] < arr[j]) {
                swap(arr,j,j+1); // >还是<可以控制浮动方向
                count++;
            }
        }
        if(count === 0) break;
    }
}
```

### 7.堆排序

<img src="http://o9qr6mev3.bkt.clouddn.com/heap.jpg" style="width: 400px;height: 300px" alt="排序" align=center />

思路：堆排序是不稳定的，在性能上稳定O(nlogN),意味着不管是有序无序都会运行这么久，空间O(1)。
1. 先将初始文件R[1..n]建成一个大根堆，此堆为初始的无序区

2. 再将关键字最大的记录R[1]（即堆顶）和无序区的最后一个记录R[n]交换，由此得到新的无序区R[1..n-1]和有序区R[n]，且满足R[1..n-1].keys≤R[n].key

3. 由于交换后新的根R[1]可能违反堆性质，故应将当前无序区R[1..n-1]调整为堆。然后再次将R[1..n-1]中关键字最大的记录R[1]和该区间的最后一个记录R[n-1]交换，由此得到新的无序区R[1..n-2]和有序区R[n-1..n]，且仍满足关系R[1..n-2].keys≤R[n-1..n].keys，同样要将R[1..n-2]调整为堆。

```js
function heapSort(arr){
    let i = arr.length /2;
    for(;i>=0;--i){
        fix(arr,i,arr.length);
    }
    for(i = arr.length-1; i>0; i--){
        swap(arr,0,i);
        fix(arr,0,i);
    }
}

/**
 * 调整堆
 * @param {Array} arr 
 * @param {number} i : 要调整的元素位置
 * @param {number} length ： 调整范围
 */
function fix(arr,i,length){
    let child ;
    for(; 2*i+1 < length; i=child){
        child = 2*i+1;
        if(child < length-1 && arr[child+1]>arr[child])child++;
        if(arr[i]<arr[child]){
            swap(arr,i,child);
        }
        else break;
    }
}
```

### 8.堆排序、快速排序、堆排序性能比较

#### 对长度为1000的随机数组，排序100个的时间：

    希尔：65ms
    快速：5ms
    堆排序：7ms
    归并：114ms (归并在实现过程中数组的切分可能造成了性能损耗)

#### 对排好序的数组进行排序：

    堆排序:7ms
    快速：78ms (此时是快速的最差情况O(N^2))
    希尔：36ms 
    归并：106ms
测试代码：

```js
function random(){
    return ~~(Math.random()*10000);
}
function randomArr(length){
    let res = [], i =0;
    while(i<length){
        res.push(random());
        i++;
    }
    return res;
}
let time = 0;
for(let i = 0; i<100; i++){
    let arr = randomArr(1000);
    //quickSort(arr); //先用快排排序，测试排好序的数组排序
    let start = new Date();
    quickSort(arr);
    let end = new Date();
    time += end - start;
}
console.log(time)
```

### 参考

[排序算法稳定性](https://baike.baidu.com/item/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E7%A8%B3%E5%AE%9A%E6%80%A7/9763250?fr=aladdin)

[视觉直观感受](http://blog.jobbole.com/11745/)

[三种快速排序以及快速排序的优化](http://blog.csdn.net/insistgogo/article/details/7785038)
