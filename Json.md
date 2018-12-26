# Json

## Json的坑

默认情况下encoding会进行html转义，并且在行末加上"\n"，所以必须有以下两个设置来关闭默认的这两个设置

```
func JSONMarshal(t interface{}) ([]byte, error) {
	buffer := &bytes.Buffer{}
	encoder := json.NewEncoder(buffer)
	encoder.SetIndent("", "")
	encoder.SetEscapeHTML(false) // 默认情况下encoding会进行html转义，现在设置关闭它
	err := encoder.Encode(t)  // 在这里，源码里的encode函数会默认在后面加上\n，所以需要在下面去掉这个\n
	return strings.Trim(string(buffer.Bytes()),"\n"), err
}
```
