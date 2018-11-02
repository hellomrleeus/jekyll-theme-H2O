---
layout: post
title: 'PHP:常见排序'
date: 2018-11-02
categories: PHP
tags: PHP
author: 李昕
---

一、冒泡排序

冒泡排序理解起来是最简单，但是时间复杂度（O(n^2)）也是最大的之一，实现代码如下：

```php
function bubbleSort($arr) {
    $len = count($arr);
    for ($i = 0; $i < $len; $i++) {
        // 遍历i后面的元素，只要该元素小于当前元素，就把较小的往前冒泡
        for ($j = $i + 1; $j < $len; $j++) {
            if ($arr[$i] > $arr[$j]) {
                $t = $arr[$i];
                $arr[$i] = $arr[$j];
                $arr[$j] = $t;
            }
        }
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(bubbleSort($arr));
```

二、选择排序 

选择排序理解起来也比较简单，时间复杂度也是O(n^2)，实现代码如下：

```php
function selectSort($arr) {
    $len = count($arr);
    for ($i = 0; $i < $len; $i++) {
        $minIndex = $i;
        // 找出i后面最小的元素与当前元素交换
        for ($j = $i + 1; $j < $len; $j++) {
            if ($arr[$j] < $arr[$minIndex]) {
                $minIndex = $j;
            }
        }
        if ($minIndex != $i) {
            $t = $arr[$i];
            $arr[$i] = $arr[$minIndex];
            $arr[$minIndex] = $t;
        }
    }
    return $arr;
}
$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(selectSort($arr));
```

三、插入排序 

感觉插入排序跟冒泡排序有点相似，时间复杂度也是O(n^2)，实现代码如下：

```php
function insertSort($arr) {
    $len = count($arr);
    for ($i = 1; $i < $len; $i++) {
        if ($arr[$i] < $arr[$i - 1]) {
            $t = $arr[$i];
            $j = $i - 1;
            // 如果当前元素大于前一个元素，就往前挪
            while ($j >= 0 && $t < $arr[$j]) {
                $arr[$j + 1] = $arr[$j];
                $j--;
            }
            $arr[$j + 1] = $t;
        }
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(insertSort($arr));
```

四、希尔排序 

希尔排序其实可以理解是插入排序的一个优化版，它的效率跟增量有关，增量要取多少，根据不同的数组是不同的，所以希尔排序是一个不稳定的排序算法，它的时间复杂度为O(nlogn)到O(n^2)之间，实现代码如下：

```php
function shellSort($arr) {
    $len = count($arr);
    $stepSize = floor($len / 2);
    while ($stepSize >= 1) {
        for ($i = $stepSize; $i < $len; $i++) {
            if ($arr[$i] < $arr[$i - $stepSize]) {
                $t = $arr[$i];
                $j = $i - $stepSize;
                while ($j >= 0 && $t < $arr[$j]) {
                    $arr[$j + $stepSize] = $arr[$j];
                    $j -= $stepSize;
                }
                $arr[$j + $stepSize] = $t;
            }
        }
        // 缩小步长，再进行插入排序
        $stepSize = floor($stepSize / 2);
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(shellSort($arr));
```

五、堆排序 

堆排序是一种高效的排序算法，它的时间复杂度是O(nlogn)。原理是：先把数组转为一个最大堆，然后把第一个元素跟第i元素交换，然后把剩下的i-1个元素转为最大堆，然后再把第一个元素与第i-1个元素交换，以此类推。实现代码如下：

```php
function heapSort($arr) {
    $len = count($arr);
    // 先建立最大堆
    for ($i = floor(($len - 1) / 2); $i >= 0; $i--) {
        $s = $i;
        $childIndex = $s * 2 + 1;
        while ($childIndex < $len) {
            // 在父、左子、右子中 ，找到最大的
            if ($childIndex + 1 < $len && $arr[$childIndex] < $arr[$childIndex + 1]) $childIndex++;
            if ($arr[$s] < $arr[$childIndex]) {
                $t = $arr[$s];
                $arr[$s] = $arr[$childIndex];
                $arr[$childIndex] = $t;
                $s = $childIndex;
                $childIndex = $childIndex * 2 + 1;
            } else {
                break;
            }
        }
    }
    // 从最后一个元素开始调整
    for ($i = $len - 1; $i > 0; $i--) {
        $t = $arr[$i];
        $arr[$i] = $arr[0];
        $arr[0] = $t;
        // 调整第一个元素
        $s = 0;
        $childIndex = 1;
        while ($childIndex < $i) {
            // 在父、左子、右子中 ，找到最大的
            if ($childIndex + 1 < $i && $arr[$childIndex] < $arr[$childIndex + 1]) $childIndex++;
            if ($arr[$s] < $arr[$childIndex]) {
                $t = $arr[$s];
                $arr[$s] = $arr[$childIndex];
                $arr[$childIndex] = $t;
                $s = $childIndex;
                $childIndex = $childIndex * 2 + 1;
            } else {
                break;
            }
        }
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(heapSort($arr));
```

六、快速排序 

快排也是一个高效的排序算法，它的时间复杂度也是O(nlogn)。原理是：选择一个基准元素，然后把数组中小于这个元素的元素放在基准元素左边，大于它的，放在基准元素右边。然后对这两边继续同样的操作。代码如下：

```php
function quickSort($arr) {
    $len = count($arr);
    quickSortRecursion($arr, 0, $len - 1);
    return $arr;
}

function quickSortRecursion(&$arr, $begin, $end) {
    if ($begin < $end) {
        $left = $begin;
        $right = $end;
        $temp = $arr[$begin];   // $temp为基准元素
        // 把小于$temp的元素放到$temp左边，大于它的放在右边
        while ($left < $right) {
            while ($left < $right && $arr[$right] >= $temp) $right--;
            if ($left < $right) {
                $arr[$left++] = $arr[$right];
            }
            while ($left < $right && $arr[$left] <= $temp) $left++;
            if ($left < $right) {
                $arr[$right--] = $arr[$left];
            }
        }
        $arr[$left] = $temp;
        // 把数组分成两部分，递归调用该函数
        quickSortRecursion($arr, $begin, $left - 1);
        quickSortRecursion($arr, $left + 1, $end);
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(quickSort($arr));
```

七、归并排序 

归并排序的时间复杂度也是O(nlogn)。原理是：对于两个排序好的数组，分别遍历这两个数组，获取较小的元素插入新的数组中，那么，这么新的数组也会是排序好的。代码如下：

```php
function mergeSort($arr) {
    $len = count($arr);
    $arr = mergeSortRecursion($arr, 0, $len - 1);
    return $arr;
}

function mergeSortRecursion(&$arr, $begin, $end) {
    if ($begin < $end) {
        $mid = floor(($begin + $end) / 2);
        // 把数组不断拆分，只到只剩下一个元素，对于一个元素的数组，一定是排序好的
        mergeSortRecursion($arr, $begin, $mid);
        mergeSortRecursion($arr, $mid + 1, $end);
        $i = $begin;
        $j = $mid + 1;
        $k = 0;
        $temp = array();
        // 开始执行归并，遍历两个数组，从它们里面取较小的元素插入到新数组中
        while ($i <= $mid && $j <= $end) {
            if ($arr[$i] < $arr[$j]) {
                $temp[$k++] = $arr[$i++];
            } else {
                $temp[$k++] = $arr[$j++];
            }
        }
        while ($i <= $mid) {
            $temp[$k++] = $arr[$i++];
        }
        while ($j <= $end) {
            $temp[$k++] = $arr[$j++];
        }
        for ($i = 0; $i < $k; $i++) {
            $arr[$i + $begin] = $temp[$i];
        }
    }
    return $arr;
}

$arr = [3,1,13,5,7,11,2,4,14,9,15,6,12,10,8];
print_r(mergeSort($arr));
```