## sync.Pool

### 链接

https://www.cnblogs.com/qcrao-2018/p/12736031.html

https://mp.weixin.qq.com/s?__biz=MzA4ODg0NDkzOA==&mid=2247487149&idx=1&sn=f38f2d72fd7112e19e97d5a2cd304430&source=41#wechat_redirect

https://xargin.com/lock-contention-in-go/

什么是cpu cache https://www.jianshu.com/p/dc4b5562aad2

### 有什么用

有大量重复分配、回收内存的地方，可以用`sync.Pool`减轻GC的压力。频繁地分配、回收内存会给GC带来一定的负担，严重的时候会引起CPU的毛刺。

`sync.Pool`可以将暂时不用的对象缓存起来，待下次需要的时候直接使用，不用再经过内存分配，可复用对象的内存，减轻GC的压力，提升系统的性能。

### 适用场景

```
当多个goroutine都需要创建同一个对象的时候，如果goroutine数过多，导致对象的创建数目剧增，进而导致GC压力增大。形成“并发大-占用内存大-GC缓慢-处理并发能力降低-并发更大”这样的恶性循环。
这个时候，需要有一个对象池，每个goroutine不再自己单独创建对象，而是从对象池中获取出一个对象。
```

**关键思想就是对象的复用**，避免重复地创建、销毁对象。



### 如何使用

```go
package main

import (
	"fmt"
	"sync"
)

var pool *sync.Pool

type Person struct {
	Name string
}

func initPool() {
	pool = &sync.Pool{
		New: func() interface{} {
			fmt.Println("Creating a new Person")
			return new(Person)
		},

	}
}

func main() {
	initPool()

	p := pool.Get().(*Person)
	fmt.Println("first get from pool: ", p)

	p.Name = "first"
	fmt.Printf("set p.Name = %s\n", p.Name)

	pool.Put(p)

	fmt.Println("Pool has one object: call Get: ", pool.Get().(*Person))
	fmt.Println("Pool has no object, call Get: ", pool.Get().(*Person))
}
```



### 测试



### 源码分析

noCopy

```
noCopy是go1.7开始引入的一个静态检查机制，它不仅仅工作在运行时或标准库，同时也对用户代码有效。
开发者只需实现这样的不消耗内存、仅用于静态分析的结构，来保证一个对象在第一次使用后不发生复制。
```

victime

```
Victime Cache本来是计算机架构里面的一个概念，是CPU硬件处理缓存的一种技术，sync.Pool引入的意图在于降低GC压力，同时提高命中率。
```

