---
creation date: 2023-03-23 19:21 
---
 [[2023-03-23-星期四]]  #🌱发芽

## 官方文档
[Documentation - The Go Programming Language](https://go.dev/doc/)

## 一些常用的 go command
```go
// giving it the name of the module your code will be in. The name is the module's module path.
go mod init [name]

// The go run is one of many  commands you'll use to get things done with Go.
go run .

go help
```
##  Go 依赖管理
```go
go mod init

go get

go mod tidy
```


## Go 基础语法之旅
[包、变量和函数](https://tour.go-zh.org/basics)
函数外的每个语句都必须以关键字开始（`var`, `func` 等等），因此 `:=` 结构不能在函数外使用。
[流程控制语句：for、if、else、switch 和 defer](https://tour.go-zh.org/flowcontrol)
[更多类型：struct、slice 和映射](https://tour.go-zh.org/moretypes)

