# go func与循环

如下代码中，如果直接在go func内部使用f，会导致f在使用前就已经被更新，所以需要把f传入func内部使用

```
for _,f := range splitFiles {
		go func(fileNameSplit string) {
			dosomething(fileNameSplit)
		}(f)
	}
```