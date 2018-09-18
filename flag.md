# flag

flag的使用非常简单，只有几个容易错的点

- 不要忘记运行flag.Parse()
- flag解析出的参数必须用*引用
- flag.Bool解析出的bool型参数默认值应该指定为false,因为bool型只要在命令行中出现就会解析成true，指定为false时应该在命令行中不写此参数，所以默认值如果为true的话会导致此参数永远为true