# 错误处理

### panic和合适的recover机制


一般来说，为了反正panic错误发生时程序全部退出，我们会使用recover，并在recover中记录错误详情：

```
func main()  { 
	defer func() {
		if err := recover(); err != nil {	
			log(fmt.Sprintf("panic, err=%+v:", err))
		}
		p.close()
	}()
	
	dosomething()
}
```

但是这种写法在只能记录下错误信息，形如：“runtime error: invalid memory address or nil pointer dereference”，实际上这是一句无用的废话，尤其是当程序逻辑比较复杂时。此时使用debug.Stack()可以记录下堆栈信息，对事后debug更实用。

```
func main()  { 
	defer func() {
		if err := recover(); err != nil {
			log(fmt.Sprintf("panic, err=%+v: %s", err, debug.Stack()))
		}
		p.close()
	}()
	
	dosomething()
}
```	