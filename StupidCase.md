# 各种愚蠢案例

#### case的语法问题

下面是一个网上抄的例子（原文：http://www.lanecn.com/article/main/aid-102）

```
a := 2
switch a {
case 2:
case 3:
    fmt.Println("第一个case")
case 4, 5:
    fmt.Println("第二个case")
default:
    fmt.Println("Default")
}
```
上面case 2不会执行任何东西，switch换成select也是一样的问题，很容易被坑
