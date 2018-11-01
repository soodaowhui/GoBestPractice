# 类型

#### 调用私有类型

有一个包内的struct tLogItem，首字母为小写，所以外部无法访问它，但是实际此类型有可能被外部访问到

```
package internalPack

type (
	tLogItem struct {
		msg []byte
		tag string
	}
)

func DumpLogs() []*tLogItem {
	syslogCache := make([]*tLogItem)
	return syslogCache // syslogCache为tLogItem的slice
}
```

在一个外部的包中使用到这个struct时就需要调用这个私有类型，需要用反射的方式调用:`reflect.ValueOf(item).Elem().Field(1)`

```
package externalPack

import "internalPack"

func main(){
	logs := internalPack.DumpLogs()
	for _,item := range logs{
		tag := reflect.ValueOf(item).Elem().Field(1)
		tmpLogs[fmt.Sprint(tag)]++
	}
}

```
