# 函数

#### 可变参数函数调用


可变参数函数是指参数形如下的函数

	func receiveAnyType(args ...interface{}) {
	    for _,arg := range args {
	        fmt.Println(arg)
	    }
	}

一般来说调用的方式是这样的

	func InterfaceArg() {
	    receiveAnyType("NetworkSettings","IPAddress")
	}

但是这样无法支持变化的参数列表，也就说我事先并不知道输入是两个确定的字符串，最常见的情况是入参实际上是一个slice，但是不能直接把这个slice作为参数传入，这时候还要用到...操作符

	func InterfaceArg() {
	    interfaceArg := []string{"NetworkSettings","IPAddress"}
	    receiveAnyType(interfaceArg...)
	}
