# Generic Pool

golang通用连接池，管理所有实现了`Closer`接口的资源。

## ChangeLog

+ 添加超时处理机制，需要实现`GetActiveTime`方法返回最新活跃时间

## Get Stated

```go
type DemoCloser struct {
	Name     string
	activeAt time.Time
}

func (p *DemoCloser) Close() error {
	fmt.Println(p.Name, "closed")
	return nil
}

func (p *DemoCloser) GetActiveTime() time.Time {
	return p.activeAt
}
// 创建连接池
pool, err := NewGenericPool(0, 10, time.Minute*10, func() (io.Closer, error) {	   
	    // 模拟连接延迟
		time.Sleep(time.Second)
		return &DemoCloser{Name: "test"}, nil
})
// 从连接池获取连接
if s, err := pool.Acquire();err != nil {
	// 出错了
} else {
    // work
    // 回收连接
    pool.Release(s)
}
// 不需要的时候关闭连接池
pool.Shutdown()
```