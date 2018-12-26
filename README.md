<!-- GFM-TOC -->

- [Java排序算法代码](#java排序算法代码)
  - [零、 排序基类](#零-排序基类)
  - [一、 选择排序](#一-选择排序)
  - [二、 插入排序](#二-插入排序)
  - [三、 希尔排序](#三-希尔排序)
  - [四、 归并排序](#四-归并排序)
    - [1、 自顶向下](#1-自顶向下)
    - [2、 自底向上](#2-自底向上)
  - [五、 快速排序](#五-快速排序)
    - [1、 基本版](#1-基本版)
    - [2、 双路切分版](#2-双路切分版)
    - [3、 三路切分版](#3-三路切分版)
  - [六、 堆排序](#六-堆排序)
    - [1、 shiftUp](#1-shiftup)
    - [2、 shiftDown](#2-shiftdown)
    - [3、 heaplify](#3-heaplify)
    - [4、 MaxHeap](#4-maxheap)
    - [5、 原地堆排序](#5-原地堆排序)
  - [七、 总结](#七-总结)
    <!-- GFM-TOC -->

# Java排序算法代码

## 零、 排序基类

```java
package com.yunche.sort;

/**
 * @ClassName: BaseSort
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class BaseSort {
    public static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    public static void swap(Comparable[] a, int i, int j) {
        Comparable temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }

}
```

## 一、 选择排序

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Selection
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class Selection extends BaseSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N - 1; i++) {
            int min = i;
            for (int j = i + 1; j < N; j++) {
                if (less(a[j], a[min])) {
                    min = j;
                }
            }
            swap(a, i, min);
        }
    }

    public static void main(String[] args) {
        Comparable[] a = {1, 4, 6, 5, 2, 3, 9, 8, 7, 0};
        sort(a);
        System.out.println(Arrays.toString(a));
    }

}

```

## 二、 插入排序

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Insertion
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class Insertion extends BaseSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N - 1; i++) {
            for (int j = i + 1; j > 0 && less(a[j], a[j - 1]); j--) {
                swap(a, j, j - 1);
            }
        }
    }

    public static void main(String[] args) {
        Comparable[] a = {'S', 'O', 'R', 'T', 'E', 'X', 'A', 'M', 'P', 'L', 'E'};
        sort(a);
        System.out.println(Arrays.toString(a));
    }
}

```

## 三、 希尔排序

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Shell
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class Shell extends BaseSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while (h < N / 3) {
            // h 1 4 12
            h = 3 * h + 1;
        }

        while (h >= 1) {
            for (int i = 0; i < N - h; i++) {
                for (int j = i + h; j >= h && less(a[j], a[j - h]); j -= h) {
                    swap(a, j, j - h);
                }
            }
            h /= 3;
        }
    }

    public static void main(String[] args) {
        Comparable[] cs = {'S', 'H', 'E', 'L', 'L', 'S', 'O', 'R', 'T', 'E', 'X', 'A', 'M', 'P', 'L', 'E'};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

## 四、 归并排序

### 1、 自顶向下

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Up2DownMerge
 * @Description:自顶向下
 * @author: yunche
 * @date: 2018/12/12
 */
public class Up2DownMerge extends BaseSort {
    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        aux = new Comparable[a.length];
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        //递归边界
        if (hi <= lo) {
            return;
        }
        //优化：当hi - lo 小于一定值，可以使用插入排序来提高性能
        int mid = (lo + hi) >> 1;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        //优化：if (a[mid] <= a[mid + 1]) return;对于有序数组来说算法变成了 O(n),对于乱序的话 比较本身也会耗费时间，
        merge(a, lo, mid, hi);
    }

    private static void merge(Comparable[] a, int lo, int mid, int hi) {
        int le = lo;
        int ri = mid + 1;

        for (int k = lo; k <= hi; k++) {
            if (le > mid) {
                aux[k] = a[ri++];
            } else if (ri > hi) {
                aux[k] = a[le++];
            } else {
                if (less(a[ri], a[le])) {
                    aux[k] = a[ri++];
                } else {
                    aux[k] = a[le++];
                }
            }
        }
        //复制到原数组
        for (int k = lo; k <= hi; k++) {
            a[k] = aux[k];
        }
    }

    public static void main(String[] args) {
        Comparable[] cs = {'M', 'E', 'R', 'G', 'E', 'S', 'O', 'R', 'T', 'E', 'X', 'A', 'M', 'P', 'L', 'E'};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

### 2、 自底向上

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Down2UpMerge
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class Down2UpMerge extends BaseSort {
    private static Comparable[] aux;

    public static void sort(Comparable[] a) {
        int N = a.length;
        aux = new Comparable[N];

        for (int sz = 1; sz < N; sz += sz) {
            for (int lo = 0; lo < N - sz; lo += sz + sz) {
                merge(a, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
            }
        }
    }

    private static void merge(Comparable[] a, int lo, int mid, int hi) {
        int le = lo;
        int ri = mid + 1;

        for (int k = lo; k <= hi; k++) {
            if (le > mid) {
                aux[k] = a[ri++];
            } else if (ri > hi) {
                aux[k] = a[le++];
            } else {
                if (less(a[ri], a[le])) {
                    aux[k] = a[ri++];
                } else {
                    aux[k] = a[le++];
                }
            }
        }
        //复制到原数组
        for (int k = lo; k <= hi; k++) {
            a[k] = aux[k];
        }
    }

    public static void main(String[] args) {
        Comparable[] cs = {'M', 'E', 'R', 'G', 'E', 'S', 'O', 'R', 'T', 'E', 'X', 'A', 'M', 'P', 'L', 'E'};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

## 五、 快速排序

### 1、 基本版

![qucik.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/qucik.gif?raw=true)

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Quick
 * @Description: 快速排序基本版
 * 缺点：含有大量重复元素时，时间复杂度近乎退化为O(N?)
 * @author: yunche
 * @date: 2018/12/15
 */
public class Quick extends BaseSort {
    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }

        int p = partition(a, lo, hi);
        sort(a, lo, p - 1);
        sort(a, p + 1, hi);
    }

    private static int partition(Comparable[] a, int lo, int hi) {
        //优化：随机化标定点，避免当数组近乎有序时，快速排序退化成O(N?)
        swap(a, (int) (Math.random() * (hi - lo + 1) + lo), lo);
        //切分时使得a[lo+1..j] < v; a[j+1..i-1] > v;
        //指定j，i初始值使得上面两个集合初始化为空
        // i为当前元素, j为当前小于v的最后一个元素
        int j = lo;
        Comparable v = a[lo];
        for (int i = lo + 1; i <= hi; i++) {
            if (less(a[i], v)) {
                swap(a, i, ++j);
            }
        }
        swap(a, lo, j);
        return j;
    }

    public static void main(String[] args) {
        Comparable[] cs = {1, 4, 5, 5, 9, 4, 7, 6, 0, 3, 2, 8};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

### 2、 双路切分版

![qucik2.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/qucik2.gif?raw=true)

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Quick2Ways
 * @Description: 快速排序双路切分(将整个数组，切分成小于v，和大于v两部分，再分别递归)
 * @author: yunche
 * @date: 2018/12/13
 */
public class Quick2Ways extends BaseSort {
    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        //递归边界
        if (hi <= lo) {
            return;
        }
        int p = partition(a, lo, hi);
        sort(a, lo, p - 1);
        sort(a, p + 1, hi);
    }

    private static int partition(Comparable[] a, int lo, int hi) {
        //优化：随机化标定点，避免当数组近乎有序时，快速排序退化成O(N?)
        swap(a, (int) (Math.random() * (hi - lo + 1) + lo), lo);
        //定义变量i，使得a[lo+1...i-1] < v
        //定义变量j，使得a[j...hi] > v
        int i = lo, j = hi + 1;
        Comparable v = a[lo];
        while (true) {
            while (less(a[++i], v) && i != hi) {
            }
            while (less(v, a[--j]) && j != lo) {
            }

            if (i >= j) {
                break;
            }
            swap(a, i, j);
        }
        swap(a, lo, j);
        return j;
    }

    public static void main(String[] args) {
        Comparable[] cs = {1, 0, 4, 5, 5, 9, 4, 7, 6, 3, 2, 1, 8};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

### 3、 三路切分版

![qucik3.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/qucik3.gif?raw=true)

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: Quick3Ways
 * @Description: 快速排序三路切分(将整个数组 ， 切分成小于v ， 等于v和大于v三部分 ， 再分别递归)
 * @author: yunche
 * @date: 2018/12/16
 */
public class Quick3Ways extends BaseSort {

    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) {
        //递归边界
        if (hi <= lo) {
            return;
        }

        //优化：随机化标定点，避免当数组近乎有序时，快速排序退化成O(N?)
        swap(a, (int) (Math.random() * (hi - lo + 1) + lo), lo);
        //定义变量lt使得a[lo+1...lt] < v
        //定义变量gt使得a[gt...hi] > v
        int lt = lo, gt = hi + 1;
        //定义变量i指向当前元素
        int i = lo + 1;
        Comparable v = a[lo];
        while (i < gt) {
            if (less(a[i], v)) {
                swap(a, i, lt + 1);
                lt++;
                i++;
            } else if (less(v, a[i])) {
                swap(a, i, --gt);
            } else {
                i++;
            }
        }
        swap(a, lo, lt);

        sort(a, lo, lt - 1);
        sort(a, gt, hi);
    }

    public static void main(String[] args) {
        Comparable[] cs = {1, 0, 4, 5, 5, 9, 4, 7, 6, 3, 2, 1, 8};
        sort(cs);
        System.out.println(Arrays.toString(cs));
    }
}
```

## 六、 堆排序

### 1、 shiftUp

![shiftup.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/shiftup.gif?raw=true)

### 2、 shiftDown

![shiftdown.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/shiftdown.gif?raw=true)

### 3、 heaplify

![heaplify.gif](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/heaplify.gif?raw=true)

### 4、 MaxHeap

```java
package com.yunche.datastructure;

/**
 * @ClassName: MaxHeap
 * @Description: 利用数组实现最大堆（完全二叉树）：任意节点的值不大于父节点
 * @author: yunche
 * @date: 2018/12/17
 */
public class MaxHeap<T extends Comparable> {

    /**
     * 存储堆中元素
     */
    private T[] data;

    /**
     * 堆中元素的个数
     */
    private int count;

    /**
     * 堆中最大容量
     */
    private int capacity;

    /**
     * 构造一个空堆，最多容纳capacity个元素
     *
     * @param capacity
     */
    public MaxHeap(int capacity) {
        this.capacity = capacity;
        count = 0;
        //数组从索引为1的位置开始存储元素
        data = (T[]) new Comparable[capacity + 1];
    }

    /**
     * 根据已有数组构建最大堆
     * 时间复杂度为O(n)
     *
     * @param arr
     */
    public MaxHeap(T[] arr) {

        capacity = arr.length;
        count = arr.length;
        data = (T[]) new Comparable[capacity + 1];
        for (int i = 1; i <= count; i++) {
            data[i] = arr[i - 1];
        }

        int k = count >> 1;
        while (k >= 1) {
            shiftDown(k);
            k--;
        }
    }

    /**
     * 返回堆中元素个数
     *
     * @return
     */
    public int size() {
        return count;
    }

    /**
     * 返回堆中是否为空
     *
     * @return
     */
    public boolean isEmpty() {
        return count == 0;
    }

    /**
     * 向最大堆中插入一个元素item
     *
     * @param item
     */
    public void insert(T item) {
        if (count == capacity) {
            capacity = capacity << 1;
            T[] tem = (T[]) new Comparable[capacity + 1];
            for (int i = 1; i <= count; i++) {
                tem[i] = data[i];
            }
            data = tem;
        }
        data[++count] = item;
        shiftUp(count);
    }

    /**
     * 从堆中取出最大元素，即堆顶元素
     *
     * @return 返回最大元素
     */
    public T extractMax() {
        if (count == 0) {
            return null;
        }
        T ret = data[1];
        data[1] = null;
        swap(1, count--);
        shiftDown(1);
        return ret;
    }

    /**
     * 将数组中索引为k的节点向上移动，直到符合最大堆的定义
     *
     * @param k 索引
     */
    private void shiftUp(int k) {
        while (k > 1 && less(data[k >> 1], data[k])) {
            swap(k >> 1, k);
            k >>= 1;
        }
    }

    /**
     * 将数组中索引为k的节点向下移动，直到符合最大堆的定义
     *
     * @param k
     */
    private void shiftDown(int k) {

        //边界：确保至少有一个孩子
        while( 2*k <= count ){
            // 在此轮循环中,data[k]和data[j]交换位置
            int j = 2*k;
            if(j+1 <= count && less(data[2 *k ], data[2 *k +1])) {
                j ++;
            }
            // data[j] 是 data[2*k]和data[2*k+1]中的最大值
            if(!less(data[k], data[j]) ) {
                break;
            }
            swap(k, j);
            k = j;
        }
    }

    /**
     * 交换堆中索引为i和j的两个元素
     *
     * @param i
     * @param j
     */
    private void swap(int i, int j) {
        T t = data[i];
        data[i] = data[j];
        data[j] = t;
    }

    private boolean less(T t1, T t2) {
        return t1.compareTo(t2) < 0;
    }

    /**
     * 测试方法
     *
     * @param args
     */
    public static void main(String[] args) {
        MaxHeap<Integer> maxHeap = new MaxHeap<Integer>(10);
        Integer[] a = {34, 12, 23, 90, 87, 16, 15 ,17, 32, 43};

        /*****************测试shiftdown开始*********/
        for (int i =0; i < a.length; i++) {
            maxHeap.insert(a[i]);
        }
        // 取出来的顺序应该是按照从大到小的顺序取出来的
        while (!maxHeap.isEmpty()) {
            System.out.print(maxHeap.extractMax() + " ");
        }
        System.out.println();
        /*****************测试shiftdown结束*********/

        /**********测试heaplify开始*************/
        MaxHeap<Integer> heap2 = new MaxHeap<Integer>(a);
        // 取出来的顺序应该是按照从大到小的顺序取出来的
        while (!heap2.isEmpty()) {
            System.out.print(heap2.extractMax() + " ");
        }
        System.out.println();
        /**********测试heaplify结束*************/

    }
}
```

### 5、 原地堆排序

![heapsort.png](https://github.com/xiaxinandye/JavaSortAlgorithm/blob/master/img/heapsort.png?raw=true)

```java
package com.yunche.sort;

import java.util.Arrays;

/**
 * @ClassName: HeapSort
 * @Description: 原地堆排序(堆中索引从0开始)--利用数组本身进行最大堆化，然后再将最大的元素放置
 * 数组末尾，再将剩余的数组元素进行shiftdown操作维持最大堆，再继续取出最大的元素，
 * 如此反复，直到最大堆中只剩下一个最小的元素
 * @author: yunche
 * @date: 2018/12/25
 */
public class HeapSort extends BaseSort {

    private static void shiftDown(Comparable[] arr, int k, int count) {

        //边界：确保至少有一个孩子
        while (2 * k + 1 < count) {
            // 在此轮循环中,arr[k]和 arr[j]交换位置
            int j = 2 * k + 1;
            if (j + 1 < count && less(arr[2 * k + 1], arr[2 * k + 2])) {
                j++;
            }
            // arr[j] 是 arr[2*k + 1]和 arr[2*k+2]中的最大值
            if (!less(arr[k], arr[j])) {
                break;
            }
            swap(arr, k, j);
            k = j;
        }
    }

    public static void sort(Comparable[] arr) {
        //heaplify
        for (int i = (arr.length - 1 - 1) / 2; i >= 0; i--) {
            shiftDown(arr, i, arr.length);
        }

        for (int i = arr.length - 1; i > 0; i--) {
            swap(arr, 0, i);
            shiftDown(arr, 0, i);
        }

    }

    public static void main(String[] args) {
        Integer[] a = {1, 7 ,32, 98, 23, 12, 87, 23, 63, 90, 52, 12, 34, 28};
        sort(a);
        System.out.println(Arrays.toString(a));
    }
}
```

## 七、 总结

|    -     | 平均时间复杂度 | 原地排序 | 额外空间 | 稳定排序 |
| :------: | :------------- | :------: | :------- | :------: |
| 插入排序 | O(n^2)         |    √     | O(1)     |    √     |
| 归并排序 | O(nlogn)       |    ×     | O(n)     |    √     |
| 快速排序 | O(nlogn)       |    √     | O(logn)  |    ×     |
|  堆排序  | O(nlogn)       |    √     | O(1)     |    ×     |

