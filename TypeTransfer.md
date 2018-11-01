# 类型转换

## int, int32, int64

直接用string(int64)这样的写法不会输出数字型字符，而是乱码

### string到int
int,err:=strconv.Atoi(string)

### string到int64
int64, err := strconv.ParseInt(string, 10, 64)

### int到string
string, err:=strconv.Itoa(int)

### int64到string
string, err:=strconv.FormatInt(int64,10)


不想要err时的简单做法：string:=fmt.Sprint(int/int64)
