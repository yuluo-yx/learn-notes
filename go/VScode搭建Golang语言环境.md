# VScode搭建Golang语言环境

****

此教程适合vscode不能手动配置go环境的gopher，因为golang官网在国外的原因，中国用户不能直接安装go的语言环境，重点描述手动配置

****

Go的SDK：[下载地址](https://golang.org/dl/)

Go的依赖包：[下载地址](https://goproxy.io/zh/)

## 开始配置

![image-20211028194139436](https://i.loli.net/2021/10/28/7pISGvY2EWCzia8.png)

**此方法只能保证临时有效，长期有效需要手动配置**

### 手动配置



配置环境变量

GO111MODULE  值为 on

GOPROXY 值为  https://goproxy.io

如果代理不好用可以换为其他的代理

## 创建项目

在vscode中创建go项目

`````````go
go mod init inid.exer/yuluo
init 后面 + 包名/x
`````````

### 出现提示之后，手动安装即可

![image-20211028200639409](../AppData/Roaming/Typora/typora-user-images/image-20211028200639409.png)

**运行HelloWorld程序**

```go
package main

import "fmt"

func main() {
	fmt.Printf("Hello world!")
}
```

**结果**

![image-20211028201010816](https://i.loli.net/2021/10/28/ETSpOyuWAGwn5dB.png)
