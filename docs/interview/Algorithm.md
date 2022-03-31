# 算法面试题

## 如何分析时间复杂度?

我们⽤⼤O表示法表示⼀下常⻅的时间复杂度量级：

常数阶O(1) 线性阶O(n) 对数阶O(logn) 线性对数阶O(nlogn) 平⽅阶O(n²)

- O(1)

传说中的常数阶的复杂度，这种复杂度⽆论数据规模n如何增⻓，计算时间是不变的。

```js
const increment = n => n++
```

不管n如何增⻓，都不会影响到这个函数的计算时间，因此这个代码的时间复杂度都是O(1)。

- O(n)

线性复杂度，随着数据规模n的增⻓，计算时间也会随着n线性增⻓。 典型的O(n)的例⼦就是线性查找

```js
const linearSearch = (arr, target) => { 
  for (let i = 0; i < arr.length; i++) { 
    if (arr[i] === target) { 
      return i 
    } 
  } 
  return -1 
}
```

线性查找的时间消化与输⼊的数组数量n成⼀个线性⽐例，随着n规模的增⼤，时间也会线性增⻓。

- O(logn)

对数复杂度，随着问题规模n的增⻓，计算时间也会随着n对数级增⻓。典型的例⼦是⼆分查找法。

```js
function binarySearch(arr, target) { 
  let max = arr.length - 1 
  let min = 0 
  while (min <= max) { 
    let mid = Math.floor((max + min) / 2) 
    if (target < arr[mid]) { 
      max = mid - 1 
    } else if (target > arr[mid]) { 
      min = mid + 1 
    } else { 
      return mid 
    } 
  }
  return -1 
}
```

在⼆分查找法的代码中，通过while循环，成 2 倍数的缩减搜索范围，也就是说需要经过 log2^n 次即可跳出循环。

事实上在实际项⽬中， O(logn) 是⼀个⾮常好的时间复杂度，⽐如当 n=100 的数据规模时，⼆分查找只需要7次，线性 查找需要100次，这对于计算机⽽⾔差距不⼤，但是当有10亿的数据规模的时候，⼆分查找依然只需要30次，⽽线性查 找需要惊⼈的10亿次， O(logn) 时间复杂度的算法随着数据规模的增⼤，它的优势就越明显。

- O(nlogn)

线性对数复杂度，随着数据规模n的增⻓，计算时间也会随着n呈线性对数级增⻓。这其中典型代表就是归并排序

- O(n²)

平⽅级复杂度，典型情况是当存在双重循环的时候，即把 O(n) 的代码再嵌套循环⼀遍，它的时间复杂度就是 O(n²) 了，代表应⽤是冒泡排序算法。

```js
function bubleSort(arra){ 
  var temp; 
  for(var i=0;i<arra.length;i++){ 
    for(var j=0;j<arra.length-i-1;j++){ 
      if(arra[j]>arra[j+1]){ 
        temp=arra[j]; arra[j]=arra[j+1]; arra[j+1]=temp; 
      } 
    } 
  }; 
  return arra; 
}
```

## 排序算法

排序算法有很多种,我们只讲最具代表性的⼏种算法： 冒泡排序、希尔排序、归并排序、快速排序

> [⼗⼤经典排序算法总结](https://github.com/damonare/Sorts)

## 冒泡排序（Bubble Sort）

实现思路: 

1. ⽐较相邻的元素。如果第⼀个⽐第⼆个⼤，就交换他们两个。 
2. 对每⼀对相邻元素作同样的⼯作，从开始第⼀对到结尾的最后⼀对。这步做完后，最后的元素会是最⼤的数。 
3. 针对所有的元素重复以上的步骤，除了最后⼀个。 
4. 持续每次对越来越少的元素重复上⾯的步骤，直到没有任何⼀对数字需要⽐较。 

实现:

```js
function bubleSort(arra){ 
  var temp; 
  for(var i=0;i<arra.length;i++){ 
    for(var j=0;j<arra.length-i-1;j++){ 
      if(arra[j]>arra[j+1]){ 
        temp=arra[j]; 
        arra[j]=arra[j+1]; 
        arra[j+1]=temp; 
      } 
    } 
  }; 
  return arra; 
}
```

改进1: 设置⼀标志性变量pos,⽤于记录每趟排序中最后⼀次进⾏交换的位置。由于pos位置之后的记录均已交换到位,故 在进⾏下⼀趟排序时只要扫描到pos位置即可。

```js
function bubbleSort2(arr) { 
  console.time('改进后冒泡排序耗时');
  var i = arr.length-1; //初始时,最后位置保持不变 
  while ( i> 0) { 
    var pos= 0; //每趟开始时,⽆记录交换 
    for (var j= 0; j< i; j++) {
      if (arr[j]> arr[j+1]) { 
        pos= j; //记录交换的位置 
        var tmp = arr[j]; 
        arr[j]=arr[j+1];
        arr[j+1]=tmp; 
      } 
      i= pos; //为下⼀趟排序作准备 
    }
  }
  console.timeEnd('改进后冒泡排序耗时'); 
  return arr; 
}
```

改进2: 传统冒泡排序中每⼀趟排序操作只能找到⼀个最⼤值或最⼩值,我们考虑利⽤在每趟排序中进⾏正向和反向两遍 冒泡的⽅法⼀次可以得到两个最终值(最⼤者和最⼩者) , 从⽽使排序趟数⼏乎减少了⼀半。

```js
function bubbleSort3(arr3) { 
  var low = 0; 
  var high= arr.length-1; //设置变量的初始值 
  var tmp,j; 
  console.time('2.改进后冒泡排序耗时'); 
  while (low < high) { 
    for (j= low; j< high; ++j) //正向冒泡,找到最⼤者 
      if (arr[j]> arr[j+1]) { 
        tmp = arr[j]; arr[j]=arr[j+1];arr[j+1]=tmp; 
      } 
    --high; //修改high值, 前移⼀位 
    for (j=high; j>low; --j) //反向冒泡,找到最⼩者 
      if (arr[j]<arr[j-1]) { 
        tmp = arr[j]; arr[j]=arr[j-1];arr[j-1]=tmp; 
      } 
    ++low; //修改low值,后移⼀位 
  }
  console.timeEnd('2.改进后冒泡排序耗时'); 
  return arr3; 
}
```

## 希尔排序(Shell Sort）

> 1959年Shell发明； 第⼀个突破O(n^2)的排序算法；是简单插⼊排序的改进版；它与插⼊排序的不同之处在于， 它会优先⽐较距离较远的元素。

### 算法描述和实现

先将整个待排序的记录序列分割成为若⼲⼦序列分别进⾏直接插⼊排序，具体算法描述：

1. 选择⼀个增量序列t1，t2，…，tk，其中ti>tj，tk=1； 
2. 按增量序列个数k，对序列进⾏k 趟排序； 
3. 每趟排序，根据对应的增量ti，将待排序列分割成若⼲⻓度为m 的⼦序列，分别对各⼦表进⾏直接插⼊排序。仅增 量因⼦为1 时，整个序列作为⼀个表来处理，表⻓度即为整个序列的⻓度。

Javascript代码实现：

```js
function shellSort(arr) { 
  var len = arr.length, temp, gap = 1; 
  console.time('希尔排序耗时:'); 
  while(gap < len/5) { //动态定义间隔序列 
    gap =gap*5+1; 
  }
  for (gap; gap > 0; gap = Math.floor(gap/5)) { 
    for (var i = gap; i < len; i++) { 
      temp = arr[i]; 
      for (var j = i-gap; j >= 0 && arr[j] > temp; j-=gap) { 
        arr[j+gap] = arr[j]; 
      }
      arr[j+gap] = temp; 
    } 
  }
  console.timeEnd('希尔排序耗时:'); 
  return arr; 
}
var arr=[3,44,38,5,47,15,36,26,27,2,46,4,19,50,48]; 
console.log(shellSort(arr));//[2, 3, 4, 5, 15, 19, 26, 27, 36, 38, 44, 46, 47, 48, 50]
```