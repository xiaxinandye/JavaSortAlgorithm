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

    public static void swap(Comparable[]a, int i, int j) {
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
public class Selection extends BaseSort{
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
 * @ClassName: Down2UpMerge
 * @Description:
 * @author: yunche
 * @date: 2018/12/12
 */
public class Down2UpMerge extends BaseSort{
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

    private static void merge(Comparable[]a, int lo, int mid, int hi) {
        int le = lo;
        int ri = mid + 1;

        for (int k = lo; k <= hi; k++) {
            if (le > mid) {
                aux[k] = a[ri++];
            } else if (ri > hi){
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

    private static void sort(Comparable[]a, int lo, int hi) {
        //递归边界
        if(hi <= lo) {
            return;
        }
        //优化：当hi - lo 小于一定值，可以使用插入排序来提高性能
        int mid = (lo + hi) >> 1;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        //优化：if (a[mid] <= a[mid + 1]) return;对于有序数组来说算法变成了 O(n),对于乱序的话 比较本身也会耗费时间，
        merge(a, lo, mid, hi);
    }

    private static void merge(Comparable[]a, int lo, int mid, int hi) {
        int le = lo;
        int ri = mid + 1;

        for (int k = lo; k <= hi; k++) {
            if (le > mid) {
                aux[k] = a[ri++];
            } else if (ri > hi){
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

