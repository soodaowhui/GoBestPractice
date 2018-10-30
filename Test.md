# 测试

## 单元测试

系统testing库使用比较简单但是缺陷很多：

- 没有断言
- 不能mock
- 表格式测试不好写

## mock test


## Redis/Http测试

涉及Redis/Http的测试主要问题是依赖外部资源，如果写死成读写本地某个资源，轻则测试用例失败，重则意外修改线上数据，所以是不可取的。

Redis测试解决办法有两种：
（Http测试的解决方法也类似，只不过使用的是httptest）

#### 用mock的方法拦截redis方法的读写，强制返回预定的值

好处是在简单情况下非常灵活，方便直接从redis方法获取复杂的结果；坏处是每一个redis方法都需要单独指定结果，而且如果有多次请求不一样返回的复杂情况很不好处理。

#### 启动测试用的fake redis server

一般我们推荐miniredis，效果是在内存中启动一个和redis使用方法几乎一样的redis实例仅供测试使用。这样的好处是可以应对几乎一切redis使用的实际情况；坏处是只能用于Do(string)方式发送的redis 指令。

```
func TestSomething(t *testing.T) {

	s, err := miniredis.Run()
	if err != nil {
		panic(err)
	}
	defer s.Close()
	
	// 把s当做真正的redis使用
}
```

