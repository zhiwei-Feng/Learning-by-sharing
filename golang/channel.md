# channel

```go
type hchan struct {
  qcount uint          // 当前队列列中剩余元素个数
  dataqsiz uint        // 环形队列长度，即可以存放的元素个数
  buf unsafe.Pointer   // 环形队列列指针
  elemsize uint16      // 每个元素的⼤⼩
  closed uint32        // 标识关闭状态
  elemtype *_type      // 元素类型
  sendx uint           // 队列下标，指示元素写⼊入时存放到队列列中的位置 x
  recvx uint           // 队列下标，指示元素从队列列的该位置读出  
  recvq waitq          // 等待读消息的goroutine队列
  sendq  waitq         // 等待写消息的goroutine队列
  lock mutex           // 互斥锁，chan不允许并发读写
}
```

分类:

1. 无缓冲channel, 读写需要成对存在
2. 有缓冲channel

值类型:

1. nil(零值), 此时读写都会阻塞
2. closed channel, 读出零值, 但写入panic
3. close closed channel, 会导致panic

Ref to: [https://studygolang.com/articles/32553](https://studygolang.com/articles/32553)
