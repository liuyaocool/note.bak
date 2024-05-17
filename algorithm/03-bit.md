# bit位

- 带符号左移( << )：<< 1 相当于 * 2
- 带符号右移( >> )
- 无符号右移( >>> )
- 与( & )：同1为1 否则为0
- 或( | )：有1为1
- 异或（ ^ ）: 相同为0,不同为1,又叫无进位相加
  - 0 ^ N = N
  - N ^ N = 0
  - a ^ b = b ^ a
  - (a^b)^c = a^(b^c)  结合律 可扩展到n个数异或

# 题: 交换两个数

```c
int a = 12;
int b = 14;
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

# 题: 一个数出现了奇数次

```c
#define ARR_LENGTH(arr) sizeof(arr)/sizeof(arr[0])

// 只有一个数出现了奇数次 找到
void findOdd()
{
    int arr[] = {1, 2, 3, 4, 4, 3, 2, 1, 3, 4, 4};
    int eor = 0;
    // printf("odd size = %lu\n", sizeof(arr)); 
    int size = ARR_LENGTH(arr);
    for (int i = 0; i < size; i++) {
        eor ^= arr[i];
    }
    printf("odd = %d\n", eor);
}
```

# 题: 最后二进制位

给一个数， 求这个数 只保留最后一个1二进制位

```c
/*
 * a   = 0 1 1 0 1 1 1 0 0 1 0 0 0 0 = a
 * ~a  = 1 0 0 1 0 0 0 1 1 0 1 1 1 1 = ~a
 * ~a+1= 1 0 0 1 0 0 0 1 1 1 0 0 0 0 = -a
 * ans = 0 0 0 0 0 0 0 0 0 1 0 0 0 0 = a & -a
 */
int a = 14;
int b = a & -a;
```

# 题: 两个数出现了奇数次

1. 全部异或

2. 得出 `eor = a ^ b (>0) `

3. 任意选定 eor 某个位置为1的数, 这里规定选最后位置

   - eor 某个位置为1, 则a b的此位置肯定不同
   - 而找到最后一个1 前边已经知道了怎么做 `eor & (-eor) `

4. 这里假设 最后一个1为 第三位

5. 可以将所有的数分为两类

   - A类: 第三位为1, 假定a在此类 则此类中所有数^ -> a
   - B类: 第三位为0, 假定b在此类

6. a,b 肯定被分散在这两类中

7. 则

   ```c
   eorp = eor ^ A类
   	// A类筛选: A中数 & (eor & (-eor)) != 0 , (eor & (-eor))=二进制最右侧1
       = eor ^ a
       = b
   ```

8. 则 ` a = eor ^ eorp `

```c
#define ARR_LENGTH(arr) sizeof(arr)/sizeof(arr[0])

int arr[] = {
    1, 1,
    2, 2,
    3, 3, 3, 3,
    4, 4, 4,
    5, 5,
    6
};
// 命名奇数次的两个变量为 a,b
int eor = 0;
int size = ARR_LENGTH(arr);
for (int i = 0; i < size; i++) {
    eor ^= arr[i];
}

int eorp = eor;
int rightOne = eor & -eor;
for (int i = 0; i < size; i++) {
    // ------- 此处自己coding写的是 >0 ----------
    if ((arr[i] & rightOne) != 0){
        eorp ^= arr[i];
    }
}
int a = eor ^ eorp;
```

# 题: a出现k次, 其他出现m次

a出现了k次, 其他都出现了m次, 且m>1,m>k, m给定, 找出a k. 空间O(1), 时间O(n)

1. 所有数 二进制位为1的 累加到 int[32] 中
2. int[32]某一位 如果是m的整数倍,则说明 此位置a的二进制为是0, 不是整数倍 a的二进制位为1
3. int[32] 遍历 %m>0 = 1, 则得到a的二进制, 得a

```c
int binLen = 32;
int t[32] = {0};  // fixed length arr, can default 0 use this mode
int zeroTimes = 0;
int result = 0;
int k = 0;

for (int i = 0; i < arrsize; i++) {
    if (arr[i] == 0) {
        zeroTimes++;
        continue;
    }
    for (int j = 0; j < binLen; j++) {
        t[j] += arr[i] >> j & 1; // 收集二进制位
    }
}

for (int i = 0; i < binLen; i++) {
    int i1 = t[i] % m;
    if (i1 != 0){
        result |= 1 << i; // | 有1为1
        k = i1;
    }
}
// 查找的数为0 是边界条件
a = result;
k = result == 0 ? zeroTimes : k;
```

