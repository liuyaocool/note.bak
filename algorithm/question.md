# 单向链表

## 题1

**题目描述**: 实现如下节点的 单向链表复制, 要求时间O(N) 空间O(1)

```java
class Node {
    int v;
    Node next; // 下一个节点
    Node rand; // 随机指向一个节点
}
```

**解题** 

1. 将复制的节点(Nx_)插入原节点(Nx)后边: ` N1 -> N1_ -> N2 -> N2_ -> ... `
2. 遍历 ` Nx_.rand = Nx.rand.next `
3. 连接复制的链表, 并将原链表复原  ` Nx_.next = Nx_.next.next `

## 题2

