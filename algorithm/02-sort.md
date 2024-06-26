# 常见排序算法对比

| 名称                  | 平均O()   | 最坏O()   | 最好O()   | 空间复杂度 | 稳定性 |
| --------------------- | --------- | --------- | --------- | ---------- | ------ |
| **基于比较的排序**    | ---------- | ---------- | `O(N*log_N)` | --------------- | --------- |
| 选择排序（Selection） | n^2       | n^2       | n^2       | 1          | 不稳   |
| 冒泡排序（Bubble）    | n^2       | n^2       | n         | 1          | 稳     |
| 插入排序（Insertion） | n^2       | n^2       | n         | 1          | 稳     |
| 堆排序（heap）        | n*log_n   | n*log_n   | n*log_n   | 1          | 不稳   |
| 希尔排序（Shell）     | n^1.3     | n^2       | n         | 1          | 不稳   |
| 归并排序（Merge）     | n*log_n   | n*log_n   | n*log_n   | n          | 稳     |
| 快速排序（Quick）     | n*log_n   | n^2       | n*log_n   | n*log_n    | 不稳   |
| **不基于比较的排序**   | ---------- | ---------- | `O(N)`    | ---------- | --------- |
| 桶排序（思想 Bucket） | n+k       | n^2       | n         | n+k        | 稳     |
| 计数排序（Counting）  | n+k       | n+k       | n+k       | n+k        | 稳     |
| 基数排序（Radix）     | n*k       | n*k       | n*k       | n+k        | 稳     |

# 选择排序：

1. 0~n-1 最小放到0
2. 1~n-1 最小放到1
3. 。。。

# 冒泡排序：

1. 0~1、1~2、2~3、。。n-2~n-1 大的后放
2. 0~1、1~2、2~3、。。n-3~n-2 大的后放
3. 。。。

# 插入排序：

有点像摸牌，前边已经排好了，新牌从后往前排插入

1. 0~1 ：1位置与前边比较，前一个比自己大 往前换，直到前边<=自己 停止
2. 0~2 ：2位置与前边比较，。。。
3. 0~3 ：3位置与前边比较，。。。
4. ...

# 希尔排序： 间隔插入排序

-  间隔序列Knuth： h=1 h=3*h+1=4 h=3*4+1=13 ...
- 例如：间隔取4， 则取 i%4 == 0 的为一组排序（仍在原数组，只不过下标间隔为4），排完之后在以间隔为2的排序，最终以间隔为1排序
  - 即 先将 0，4，8，12，。。。位置进行插入排序
  - 再将 0，2，4，6，8，。。。位置进行插入排序
  - 最后将 0，1，2，3，4，。。。位置进行插入排序

# 归并排序: O(N*logN)

两个子数组（对半劈）排好顺序，然后合并到新数组，递归子数组

过程：3个指针 两个子数组指针 一个新数组指针，2个子数组指针后移比较 放到新数组 新数组指针后移

master = 2 * T(N/2) + O(N);  两半为 T(N/2), O(N) 为merge

## 递归版



## 迭代版

- 定义步长=1 遇到步长为左组 再遇到步长为右组 进行merge
- 步长 *= 2 再merge
- 步长 *= 2 再merge
- ...

- 例如9个数 {3,2,4,6,7,1,9,5,8}
- 步长=1: 3-2 4-6 7-1 9-5 8 -> 23 46 17 59 8
- 步长=2: 23-46   17-59   8 -> 2346  1579  8
- 步长=4: 2346-1579       8 -> 12345679    8
- 步长=8: 12345679-8        -> 123456789

注意 int 有越界问题 步长<Integer.MAX 步长*2>Integer.MAX

## 题

```

面试题
1 小核 每个位置的数左侧比他小的数累加 再计算累加的累加
2 逆序对
3 >=
```


# TIM SORT

java内部 对象排序（要求稳定）使用的是改进的归并排序 叫TIM SORT

过程：多路归并 先分多块 然后两两归并 再两两归并 。。。

# 快速排序：单轴快排

过程：找到一个轴，小于轴的放到左边大于轴的放到右边

# 双轴快排：

Arrays.sort(基础数据类型)

# 堆排序

预习: 大跟堆 小跟堆 见数据结构

```java
public void heapSort(int arr[]) {
    // 1. 转换大跟堆
    // 方式1: 下沉 O(n*log_n)
    // for (int i = 0; i < arr.length; i++) {
    //     heapInsert(arr, i);
    // }
    /*
     * 方式2: 上浮 O(n)
     *      树        当前层时间复杂度
     *       x          1 * log_n    
     *     /   \        ...
     *    x     x       n/4 * 2   --每上一层 数量/2 下浮次数+1
     *   / \   / \
     *  x   x x   x     n/2 * 1   --当前层约等于
     * 
     * 得 O(n) = n/2*1 + n/4*2 + n/8*3 ....
     *                \       \
     *   2O(n) = n*1 + n/2*2 + n/4*3 ....
     * 错位相减
     *    O(n) = n + n/2 + n/4 + ... n/(n-1 or n);
     * ==>
     *    O(n) = n;
     */
    for (int i = arr.length - 1; i >= 0; i--) {
        heapify(arr, arr.length, i);
    }

    // 2. 堆上头节点跟最后一个元素交换， 然后下浮
    int heapSize = arr.length;
    do {
        swap(arr, --heapSize, 0);
        heapify(arr, heapSize, 0);
    } while (heapSize > 0);
}
```

# 桶排序: 一种基于容器思想的排序

## 计数

数据在一定范围, 范围内每个元素设置一个容器, 容器内进行统计

### 例子1: 年龄排序

1. 人类年龄已知最高不超过300岁, 给定一个长度300的数组` int arr = new int[300] `
2. 遍历给的数组, 每遍历一个数， 将 ` arr[年龄]++ `
3. 遍历数组, 即可排序

## 基数排序

### 例子1: 一堆数排序

1. 给定数组 `int arr = {135, 135, 12, 5, 6, 1, 75} `
2. 遍历得到最大数的位数: 135 -> 3
3. 原数组不足3位的 前边补0, 得 `int arr = {135, 135, 012, 05, 06, 01, 075，036，066} `
4. 准备10个桶(容器) 0~9
5. 遍历数组 个位是几 放到第几个桶, 然后从第0个桶开始, 取出, 先进先出(稳定)
6. 十位重复, 百位重复, 最终可排序完成

### 代码, 优化无桶版

画图解释
```
   桶0    排序后下标      桶1    排序后下标    

|  n1  |     0       |  n8  |     7
|  n2  |     1       |  n9  |     8                 [digitCount]
|  n3  |     2       ...                   0  1  2  3  4  5  6  7  8  9   下标
|  n4  |     3                       -->  [7, 9, 0, 0, 0, 0, 0, 0, 0, 0]  前n项和
|  n5  |     4                             |   \                       or 前n个桶内数的总计
|  n6  |     5                             |    \
|  n7  |     6                             |     \
        ||                                /       \
        \/                n7下标是6 是不是=7-1       \
  0   1   2   3   4   5   6   7   8                 \
[n1, n2, n3, n4, n5, n6, n7, n8, n9]  n9下标是8 是不是=9-1
```

```java
public static void radixSort(int[] arr) {
    if (null == arr || arr.length < 2) return;
    radixSort(arr, 0, arr.length-1, maxDigit(arr));
}

/**
 * 核心方法: 将arr 在 [L, R] 范围上排序
 * 说明: 优化核心在 digitCount 数组
 * @param arr
 * @param L
 * @param R
 * @param digit 最大数的数位
 */
private static void radixSort(int[] arr, int L, int R, int digit) {
    final int radix = 10;

    int[] sort = new int[R - L + 1];
    // 某一位“数”的暂存, 为了减少sort.length个getDigit的时间, 空间换时间
    int[] n = new int[arr.length];
    for (int d = 0; d < digit; d++) {
        // 1. 10进制数只有10个数
        int[] digitCount = new int[10];
        // 2. 将d数位的数组 在 digitCount上做统计
        for (int i = L; i <= R; i++) {
            n[i] = getDigit(arr[i], d); // 从个位开始 向前d个数位的数
            digitCount[n[i]]++;
        }
        // 3. 求digitCount 前n项和
        for (int i = 1; i < digitCount.length; i++) {
            digitCount[i] = digitCount[i] + digitCount[i-1];
        }
        // 4. 根据前n项和 统计出的数量, 从后向前遍历原数组
        //  解释: 可从前n项和中 获得原数字在排序后数组中的下标
        //  从后往前是因为 digCount-- 也是逆向, 可稳定
        for (int i = R; i >= L; i--) {
            sort[--digitCount[n[i]]] = arr[i];
        }
        System.arraycopy(sort, 0, arr, L, sort.length);
    }
}

// 获得从个位开始, 向前数 d 位的数位的 数字
private static int getDigit(int i, int d) {
    return i / ((int) Math.pow(10, d)) % 10;
}

// 求数组中最大数的数位
private static int maxDigit(int[] arr) {
    int max = Integer.MIN_VALUE;
    for (int i : arr) {
        max = Math.max(max, i);
    }
    int bits = 0;
    while (max != 0) {
        bits++;
        max /= 10;
    }
    return bits;
}

```