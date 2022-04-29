# 底层数据结构

![对应关系](<../.gitbook/assets/image (2).png>)

从上图我们可以知道, Redis底层数据结构大致有以下几种

1. 简单动态字符串 -> String
2. 双向链表 -> List
3. 压缩列表 -> \[List, Hash, Sorted Set]
4. 哈希表/字典 -> \[Hash, Set]
5. 跳表 -> Sorted Set
6. 整数集合 -> Set

### 1. 简单动态字符串sds

![数据结构](<../.gitbook/assets/image (1).png>)

其中, free用于表示buf中有多少个位置是可以插入新字符, len表示当前buf中有效字符长度, buf是存放字符串的字符数组.

相比于普通的字符串, 这种结构有以下优势:

1. 预分配buf数组和惰性空间释放(free字段),可以减少字符串修改时的内存分配次数
2. 获取长度是常数级时间复杂度
3. 根据长度预先检查内存是否安全, 避免缓冲区溢出



### 2. 整数数组intset

```c
typedef struct intset {
    uint32_t encoding;
    uint32_t length;
    int8_t contents[];
} intset;
```

其中, encoding定义整数的编码格式(比如, 用2个字节编码16位整数), intset在使用过程保持有序, 因此将其作为set的底层数据结构的时候, set的查找是O(logn)的.

intset只有在数据规模较小的情况下才被用于set的底层数据, 当数据规模较大时将使用哈希表/字典.\


Ref to:

1\. [Redis详解（四）------ redis的底层数据结构 ](https://www.cnblogs.com/ysocean/p/9080942.html)

2\. [02 | 数据结构：快速的Redis有哪些慢操作？](https://time.geekbang.org/column/article/268253)

3\. [Redis内部数据结构详解](http://zhangtielei.com/posts/blog-redis-dict.html)

