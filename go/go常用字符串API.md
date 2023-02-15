# go常用字符串API

1.长度len

```go
str := "hello北京"
// golang的编码统一为utf8
// ASCII码占一个字节
// 汉字占3个字节（byte类型的别名是uint8）
fmt.Println("str len=", len(str))
```
2.string遍历，转[]rune （rune是int32类型的别名，主要用于表示一个字符类型大于一个字节小于等于4个字节的情况下，特别是**中文字符。**）

```go
str := "hello北京"
r := []rune(str)
for i := 0; i < len(r); i++ {
	fmt.Printf("char= %c\n", r[i])
}
```
3.字符串转int

```go
n, err := strconv.Atoi("hel")
if err != nil {
	fmt.Println(err)
} else {
	fmt.Println(n)
}
```
4.int to string

```go
str := strconv.Itoa(1234)
fmt.Printf("str=%v, str type is %T", str, str)
```
5.字符串转[]byte

```go
var bytes = []byte("hello go")
fmt.Printf("bytes=%v\n", bytes)
```
6.[]byte 转 string

```go
str := string([]byte{97, 98, 99})
fmt.Printf("str=%v\n", str)
```
7.十进制转
返回string

```go
str := strconv.FormatInt(123, 2)
fmt.Printf("二进制 %v\n", str)
str = strconv.FormatInt(123, 16)
fmt.Printf("十六进制 %v\n", str)
```
8.查找子串是否在指定string中

```go
a := strings.Contains("seafood", "foo")
fmt.Println(a)
```
9.统计string中，有几个指定的子串

```go
n := strings.Count("heeese", "ee")
fmt.Println(n)
```
不能重叠
10.不区分大小写的字符串比较

```go
fmt.Println("abc" == "ABC") // false
fmt.Println(strings.EqualFold("abc", "ABC")) // true
```
11.返回子串在string中第一次出现的index
没有返回-1

```go
index := strings.Index("hello abcabc", "abc")
fmt.Println(index)
```
12.返回子串最后一次出现的index
没有返回-1

```go
index := strings.LastIndex("hello abcabc", "abc")
fmt.Println(index)
```
13.将指定子串替换为另一个
n可以指定替换几个，-1表示全部替换
传入的参数本身没有变化

```go
str := strings.Replace("go go hello", "go", "beijing", 4)
fmt.Println(str)
```
14.以某个指定的字符为delim
将一个string拆分为string数组

```go
strArr := strings.Split("hello,world,go", ",")
fmt.Println(strArr)
for i := 0; i < len(strArr); i++ {
	fmt.Println(strArr[i])
}
```
15。大小写转换

```go
str := "Hello GoLang 123"
fmt.Println(strings.ToLower(str))
fmt.Println(strings.ToUpper(str))
```
16.去掉string两边的空格

```go
str := "   Hello GoLang  "
fmt.Printf("%q", strings.TrimSpace(str))
```
17.去掉两边特定字符

```go
str := "a2Hello GoLanga2"
fmt.Printf("%q", strings.Trim(str, "a2"))
```
18.去掉左边指定字符
TrimLeft
19.去掉右边指定字符
TrimRight

20.判断是否以指定string开头

21.判断是否以指定string结束

```go
a := strings.HasPrefix("ftp://192", "ftp")
fmt.Println(a)
a = strings.HasSuffix("ftp://192", "ftp")
fmt.Println(a)
```
