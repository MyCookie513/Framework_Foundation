### GO 中的Happen-Before



* 目标是想对公共变量a的赋值先于对a的赋值
* 关键点： **如果一个通道读取不到数据<-c ,则会陷入一直等待的情况，知道有新的数据发送来**
* 关键点： **如果一个通道是一个无缓冲的，此时通道会等待（因为他没有地方放数据啊），直到有人接受数据，完成这个数据的交接手续，’互‘字代表了这个过程，然后这个goroutine才能继续向下进行；**



##### 1. 无论是有缓冲还是无缓冲的通道，在通道中写入数据总是happen-before于从通道中读取数据

##### 2.关闭通道的操作（关闭通道会向通道中发送一个0）是HappenBefore于从通道中接受0；

```go
实例一：
var a string
var c = make(chan int, 10)

func f() {
    a = "hello, world" //1
    c <- 0             //2
}

func main() {
    go f()   //3
    <-c      //4
    fmt.Print(a) //5
}

```

```go

实例二：
var c = make(chan int)
var a string

func f() {
    a = "hello, world" //1
    <-c                //2
}

func main() {
    go f()       //3
    c <- 0       //4
    fmt.Print(a) //5
}
```





### LOCK中的Happen-Before原则

*  sync.Mutex  和sync.RWMutex 两种锁类型 
* 这个锁只有在Unlock之后才能进行Lock ，如果没有Unlock就进行Lock则进入等待状态；

```go
package main

import (
    "fmt"
    "sync"
)

var l sync.Mutex
var a string

func f() {
    a = "hello, world" //1
    l.Unlock()         //2
}

func main() {
    l.Lock()     //3
    go f()       //4
    l.Lock()     //5
    fmt.Print(a) //6
}
```



### sync.once（进行初始化的函数，只能进行初始化一次，如果正在初始化中，则其他的goroutine等待，否则直接跳过）

```go

var a string
var once sync.Once
var wg sync.WaitGroup

func setup() {
	time.Sleep(time.Second * 2) //1
	a = "hello, world"
	fmt.Println("setup over") //2
}

func doprint() {
	once.Do(setup) //3
	fmt.Println(a) //4
	wg.Done()
}

func twoprint() {
	go doprint()
	go doprint()
}

func main() {
	wg.Add(2)
	twoprint()
	wg.Wait()

}

```





### 在这里要说明一点（可见性）： 

* GO中虽然没有volitile关键字的特性但是可以保证在下面的条件下对一个变量的写操作w1对读操作r1可见：
  - 写操作w1先于读操作r1
  - 任何对变量的写操作w2要先于写操作w1或者晚于读操作r1



### GO 中的CAS的实现

```go

var (
	counter int32          //计数器
	wg      sync.WaitGroup //信号量
)

func main() {

	threadNum := 500

	//1. 五个信号量
	wg.Add(threadNum)

	//2.开启5个线程
	for i := 0; i < threadNum; i++ {
		go incCounter(i)
	}

	//3.等待子线程结束
	wg.Wait()
	fmt.Println(counter)
}

func incCounter(index int) {
	defer wg.Done()

	spinNum := 0
	for {
		//2.1原子操作
		old := counter
		ok := atomic.CompareAndSwapInt32(&counter, old, old+1)
		if ok {
			break
		} else {
			spinNum++
		}
	}
	fmt.Printf("thread,%d,spinnum,%d\n",index,spinNum)

}

```





### 字符串的相关操作

* 如果字符串是utf8类型的话，获取所占的内存大小和字符的数量

  + 获取所占的多少个字节 ： len(s)
  + 获取内涵的字符的数量 ： utf8.RuneCountInString(s)

* 两种不同的遍历string的方式

  + i 为每个字符字节数的起点，

  ```go
    for i,b := range s{
  		fmt.Printf("%d,%c",i,b)
  	}
  ```

  + i 为每个字符的索引

  + 这个地方是重新开了每个rune四个字节的内存，而不是换一种解释方法重新解释这段内存

  + ```go
    for i,c :=range []rune(s){
    		fmt.Printf("(%d,%c)  ",i,c)
    	}
    ```

    

