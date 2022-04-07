# WaitGroup

- [WaitGroup](#waitgroup)
  - [函数](#函数)
  - [wait时如何实现超时](#wait时如何实现超时)

WaitGroup用于等待一组线程的结束。父线程调用Add方法来设定应等待的线程的数量。每个被等待的线程在结束时应调用Done方法。同时，主线程里可以调用Wait方法阻塞至所有线程结束。 


## 函数

- `func (wg *WaitGroup) Add(delta int)`
- `func (wg *WaitGroup) Done()`
- `func (wg *WaitGroup) Wait() `


示例如下：

``` go
func main() {
    var wg sync.WaitGroup

    wg.Add(1)
    go func() {
        defer wg.Done()
        fmt.Println("1 goroutine sleep ...")
        time.Sleep(2e9)
        fmt.Println("1 goroutine exit ...")
    }()

    wg.Add(1)
    go func() {
        defer wg.Done()
        fmt.Println("2 goroutine sleep ...")
        time.Sleep(4e9)
        fmt.Println("2 goroutine exit ...")
    }()

    fmt.Println("waiting for all goroutine ")
    wg.Wait()
    fmt.Println("All goroutines finished!")
}
```

异常：

要注意Add和Done函数一定要配对，否则可能发生死锁，所报的错误信息如下：

```
fatal error: all goroutines are asleep - deadlock!
```


WaitGroup在需要等待多个任务结束再返回的业务来说还是很有用的，但现实中用的更多的可能是，先等待一个协程组，若所有协程组都正确完成，则一直等到所有协程组结束；若其中有一个协程发生错误，则告诉协程组的其他协程，全部停止运行（本次任务失败）以免浪费系统资源。 
该场景WaitGroup是无法实现的，那么该场景该如何实现呢，就需要用到通知机制，其实也可以用channel来实现，具体的解决办法，请看后续的文章。 
这样说来，WaitGroup的使用场景是有限的。



## wait时如何实现超时

``` go
wg := &sync.WaigGroup{}
select {
case <-wg.Wait():
// All done!
case <-time.After(500 * time.Millisecond):
// Hit timeout.
```