# 数据结构

1. 百度 Data Structure
2. geeksforgeeks.org
3. visualgo.net/zh

存储数据的不同方式 -- 数组,链表

- HashMap： 
  - 数组+链表(jdk 1.7) 
  - 数组+链表+红黑树(jdk 1.8)
    - 链表>=7 使用红黑树 泊松分布
- LinkedHashMap： 链表
- TreeMap： 红黑树

## ArrayList 动态扩容

## 连续数组

## AVL树

严格平衡二叉树

## 红黑树

## B-Tree

## B+Tree

# 单向链表

` n1 -> n2 -> n3 -> n4 `

## 判断有环并返回入环节点

1. 快慢指针, 快指针一次两步, 慢指针一次一步
2. 指针相遇, 快指针指向头部并一次一步
3. 指针二次相遇 即入环节点

# 二叉数

## 类

```java
public class Node<T> {
   public T v;
   public Node<T> left;
   public Node<T> right;
   public Node(T v) { this.v = v; }
   public Node(T v, Node<T> left, Node<T> right) {
      this.v = v;
      this.left = left;
      this.right = right;
   }
}

```

## 先序遍历: 头 左 右

```
     a
   /   \
  b     c      --> a b d e c f g
 / \   / \
d   e f   g
```

```java
public void printPreorder(Node head) {
   if (null == head) return;
   System.out.print(head.v);
   printPreorder(head.left);
   printPreorder(head.right);
}
```

## 中序遍历: 左 头 右

```
     a
   /   \
  b     c      --> d b e a f c g
 / \   / \
d   e f   g
```

```java
public void printInOrder(Node head) {
   if (null == head) return;
   printInOrder(head.left);
   System.out.print(head.v);
   printInOrder(head.right);
}
```

## 后序遍历: 左 右 头

```
     a
   /   \
  b     c      --> d e b f g c a
 / \   / \
d   e f   g
```

```java
public void printPostorder(Node head) {
   if (null == head) return;
   printPostorder(head.left);
   printPostorder(head.right);
   System.out.print(head.v);
}
```

## 递归序

```
     a
   /   \
  b     c      --> 
 / \   / \
d   e f   g
```

## 按层遍历

```
     a
   /   \
  b     c      --> a b c d e f g
 / \   / \
d   e f   g
```

```java
// setp 先将 head(a) 进队列, a出队列, 将出队a的左、右节点依次进队列, 然后再出队列 ...
public void printByLevel(Node head) {
   Queue<Node> q = new LinkedList<>();
   q.add(head);
   Node n;
   do {
      n = q.poll();
      System.out.print(n.v); // 执行操作
      if (null != n.left) q.add(n.left);
      if (null != n.right) q.add(n.right);
   } while (!q.isEmpty());
}
```


# 完全二叉树

除最后一层， 所有上层全部饱满， 且最后一层从第一个到最后一个之间 中间没有空余

如:

```
     2
   /   \
  1     3     是
 / \   / \
5   6 7   8

     2
   /   \
  1     3     是
 / \ 
5   6

     2
   /   \
  1     3     不是
 / \   / \
5   6     8
```

## 大跟堆、小跟堆

完全二叉树的 所有子树的跟(头)节点 是整个子树的最大值, 即大跟堆; 反之 是最小值 是小跟堆.

```java
// java中 此类 默认是小跟堆， 通过不同构造可实线大跟堆
PriorityQueue<Integer> heap = new PriorityQueue<>();
heap.peek(); // 获得头
```

如:

```
     8
   /   \
  5     7     是 大跟堆
 / \   / \
4   1 3   6

     8
   /   \
  8     7     是 大跟堆
 / \   / \
4   1 3   

     8
   /   \
  1     3     不是 大跟堆: 1 -> 5,6中 1不是当前子树的最大值 
 / \   / \
5   6 2   1
```

## 数组实现大跟堆

**数组下标图示**

```
数组下标: [0, 1, 2, 3, 4, 5, 6， null...]
heapSize = 7;

     0
   /   \
  1     2     下标
 / \   / \
3   4 5   6

知道某一位置下标i, 则
左子节点下标 = 2 * i + 1
右子节点下标 = 2 * i + 2
父节点下标  = (i - 1) / 2

下标计算图示:
       (i-1)/2
      /       \
     i        i+1
  /     \
2*i+1  2*i+2

   (i-1)/2
  /       \
i-1        i
        /     \
      2*i+1  2*i+2

```

**代码**

```java
public void swap(int[] arr, int index1, int index2) {
	arr[index1] ^= arr[index2];
	arr[index2] ^= arr[index1];
	arr[index1] ^= arr[index2];
}

/**
 * 上浮 
 * 时间复杂度: O(log_n), 数的层数是 log_n, 最多需要上浮 log_n层
 * @param arr 数组模拟的堆
 * @param index 新数的位置
 */
public void heapInsert(int[] arr, int index) {
   int parentIndex;
   while (arr[index] > arr[parentIndex = (index - 1) / 2]) {
      swap(arr, index, parentIndex);
      index = parentIndex;
   }
}

/**
 * 下沉
 * 时间复杂度: O(log_n)
 * @param arr 数组模拟的堆
 * @param index 新数的位置
 */
public void heapify(int[] arr, int heapSize, int index) {
   int li = 2 * index + 1, ri = li + 1;
   while (li < heapSize) {
      // 有右孩子 且 右孩子大于左孩子, 取右孩子
      li = ri < heapSize && arr[li] < arr[ri] ? ri : li; // 数大的孩子
      if (arr[li] < arr[index]) {
            break;
      }
      swap(arr, li, index);
      index = li;
      li = 2 * index + 1;
      ri = li + 1;
   }
}
```

# 前缀树

- 如 `Srting[] strs = {"abc", "abd", "bcd", "abde", "badc", "cacd"};`
- 将字符串的每个字母遍历加到树上, 通过某一节点 则此节点pass++, 若结尾则end++, 最终得到下图

![](img/datastruct_pretree.png)

> 说明: pass=通过此节点的数量 end=以此节点结尾的数量

## 优势

1. 可以快速获得以某个字符串为前缀的字符串个数
2. 可以快速获得相同字符串个数


## 代码

> delete 末尾节点没数据需要删除节点, 因为如果操作太多 不删除会存在很多无效节点 导致内存占用过大


# Hash表   (k,v)表

HashMap  增删改查 都是O(1) 但常数时间是比较大的

1. 按值传递：
   - String，Integer等包装类， 。。。
   - k，v都为String， 则hash表这条数据为 两个String相加的字节数
2. 按引用传递：非原生类型，如 new Person()， k为内存地址
   - k，v都为引用， 则hash表这条数据为 8+8=16字节

# 有序表

TreeMap  增删改查 都是logN

要求 key一定是可以比较的

1. firstKey   lastKey
2. floorKey(key)    -- <=key的最近的key
3. ceilingKey(key)    -- >=key的最近的key

# 跳表

ConcurrentSkipListMap

![](img/algorithm_skip-table.png)

多加了索引链表
