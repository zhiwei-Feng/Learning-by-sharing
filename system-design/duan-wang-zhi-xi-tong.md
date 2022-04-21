---
description: 如何设计一个短网址服务(TinyURL)？
---

# 短链接系统

## 1. 长度

短网址每一位由一个62进制数表示(0-9的10个, a-z的26个, A-Z的26个), 因此7位足以表示$$62^7 = 3521 billion$$ 个网址, 远远超过当前世界上的所有网址总和.

## 2. 映射

一个短网址只能映射到一个长网址, 但是可以有多个短网址映射到同一个长网址

## 3. 生成短网址

短网址的生成必须是不可重复的. 从这一点我们想到用类似分布式ID的方式去生成该短网址.

### 3.1 hash方式

比如MurmurHash算法来将长网址转为32,64,128bit的数字, 如

```go
import "fmt"
import "github.com/spaolacci/murmur3"

func main() {
	input := "user/role/1"
	// convert longURL to hash
	hash := murmur3.Sum32([]byte(input))
	idxToByte := []byte{}
	for i := 0; i < 10; i++ {
		idxToByte = append(idxToByte, byte('0'+i))
	}
	for i := 'a'; i < 'z'; i++ {
		idxToByte = append(idxToByte, byte(i))
	}
	for i := 'A'; i < 'Z'; i++ {
		idxToByte = append(idxToByte, byte(i))
	}
	fmt.Println(hash)
	// convert hash to tinyURL
	tinyURL := []byte{}
	for hash > 0 {
		idx := hash % 62
		hash /= 62
		tinyURL = append(tinyURL, idxToByte[idx])
	}
	fmt.Println(string(tinyURL))
}
```

对于hash冲突处理, 重要是通过再hash来解决, 首先查询生成的tinyURL是否被使用, 被使用加上一个标识一起进行再Hash操作, 直至生成未被使用的tinyURL.

### 3.2 UUID

### 3.3 利用自增ID

比如用8台MySQL服务器存储自增ID, 第一台从1开始自增,每次加8, 第二台从2开始自增, 依次类推, 实际过程中, 每台MySQL只需要保存当前ID自增到多少了即可, 不需要保存过往记录.如果其中有MySQL宕机并不会影响其生成ID的过程.

## 4. 存储

可以使用MySQL进行存储, 也可以使用Redis等分布式KV来存储, 存储的key为短网址, value为长网址.

