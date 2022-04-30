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

intset只有在数据规模较小的情况下才被用于set的底层数据, 当数据规模较大时将使用哈希表/字典.

### 3. 哈希表/字典dict

该种数据结构不仅用作Hash, Set这种数据类型, 而且也用于redis key的存储.使用链表来解决哈希冲突问题,使用两个哈希桶来实现哈希表扩容(Rehash). 其过程分为以下三步:

1. 在哈希表2上分配两倍哈希表1的空间
2. 重哈希哈希表1里的key到哈希表2
3. 释放哈希表1

为了避免步骤2造成长时间阻塞,redis使用渐进式的rehash,具体来说,每访问到一个key,就将该key下的桶重哈希到表2.为了避免这个过程表1中key对应桶有变动导致数据不一致,因此新的更新操作都直接在表2上进行.

### 4. 压缩列表ziplist

```
<zlbytes><zltail><zllen><entry>...<zlend>
```

* zlbytes: 表示ziplist总字节
* zltail: 最后一项entry的偏移量, 方便直接找到表尾
* zllen: 数据项的个数
* zlend: 结束标记

### 5. 跳表

![例子](<../.gitbook/assets/image (3).png>)

* 首先，每个节点肯定都有第1层指针（每个节点都在第1层链表里）。
* 如果一个节点有第i层(i>=1)指针（即节点已经在第1层到第i层链表中），那么它有第(i+1)层指针的概率为p。
* 节点最大的层数不允许超过一个最大值，记为MaxLevel。

```
// 计算随机层数
randomLevel()
    level := 1
    // random()返回一个[0...1)的随机数
    while random() < p and level < MaxLevel do
        level := level + 1
    return level
```

###

### &#x20;

Ref to:

1\. [Redis详解（四）------ redis的底层数据结构 ](https://www.cnblogs.com/ysocean/p/9080942.html)

2\. [02 | 数据结构：快速的Redis有哪些慢操作？](https://time.geekbang.org/column/article/268253)

3\. [Redis内部数据结构详解](http://zhangtielei.com/posts/blog-redis-dict.html)

