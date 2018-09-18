# 随机数的使用

典型的三种随机数生成方法的性能对比，在大量使用时有明显差距，实际应用时应该在init中初始化随机数，使用时仅rand.Intn()即可

#### 代码

```
	func init()  {
		rand.Seed(time.Now().UnixNano())
	}
	
	func main()  {
		runtime.GOMAXPROCS(2)
		start := time.Now()
		for i:=0;i<10000;i++{
			pr()
		}
		Cost := time.Since(start)
		fmt.Println("use rand.Seed only once, 10000times, cost:",Cost.Nanoseconds()/1000,"us, each time:",Cost.Nanoseconds()/10000000,"us")
	
		start2 := time.Now()
		for i:=0;i<10000;i++{
			prBySource()
		}
		Cost2 := time.Since(start2)
		fmt.Println("use rand.NewSource everytime, 10000times, cost:",Cost2.Nanoseconds()/1000,"us, each time:",Cost2.Nanoseconds()/10000000,"us")
	
	
		start3 := time.Now()
		for i:=0;i<10000;i++{
			prBySeed()
		}
		Cost3 := time.Since(start3)
		fmt.Println("use rand.Seed everytime, cost:",Cost3.Nanoseconds()/1000,"us, each time:",Cost3.Nanoseconds()/10000000,"us")
	}
	
	func pr()  {
		rand.Intn(1000)
	
	}
	
	func prBySource()  {
		r := rand.New(rand.NewSource(time.Now().UnixNano()))
		r.Intn(1000)
	}
	
	
	func prBySeed()  {
		rand.Seed(time.Now().UnixNano())
		r := rand.New(rand.NewSource(time.Now().UnixNano()))
		r.Intn(1000)
	}
```

#### 运行结果

```
use rand.Seed only once, 10000times, cost: 391 us, each time: 0 us
use rand.NewSource everytime, 10000times, cost: 90824 us, each time: 9 us
init rand.Seed everytime, cost: 166468 us, each time: 16 us
```