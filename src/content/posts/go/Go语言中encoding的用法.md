---
title: Go语言中encoding的用法——Binary
published: 2025-02-12
description: 'encoding/binary是Go语言标准库中的一个包，用于实现数据的二进制编码和解码。这个包主要提供了将数据以大端或小端格式进行编码和解码的功能。'
image: './liuying.jpeg'
tags: [go, 学习, encoding]
category: 'go'
draft: false 
---


`encoding/binary`是Go语言标准库中的一个包，用于实现数据的二进制编码和解码。这个包主要提供了将数据以大端或小端格式进行编码和解码的功能。

### 1. `binary.BigEndian` 和 `binary.LittleEndian`
这两个变量是`binary`包提供的两个实现了`binary.ByteOrder`接口的结构体，分别用于表示大端和小端字节序。
### 2. `Read` 方法
`Read`方法用于从字节切片中读取指定类型的数据。例如：
```go
var data []byte = []byte{0x01, 0x02, 0x03, 0x04}
var value uint32
err := binary.BigEndian.Read(data, &value)
if err != nil {
    log.Fatal(err)
}
fmt.Println(value) // 输出: 16909060
```
### 3. `Write` 方法
`Write`方法用于将数据写入到字节切片中。例如：
```go
var buffer []byte = make([]byte, 4)
var value uint32 = 16909060
err := binary.BigEndian.Write(buffer, value)
if err != nil {
    log.Fatal(err)
}
fmt.Println(buffer) // 输出: [1 2 3 4]
```
### 4. `Uvarint` 和 `Varint`
这两个函数用于处理变长整数（variable-length integers）。`Uvarint`用于解码一个无符号的变长整数，`Varint`用于解码一个有符号的变长整数。
```go
buf := []byte{0x96, 0x01}
value, n := binary.Uvarint(buf)
if n <= 0 {
    log.Fatal("Uvarint error")
}
fmt.Println(value) // 输出: 150
```
### 5. `PutUvarint` 和 `PutVarint`
这两个函数用于将变长整数编码为字节切片。`PutUvarint`用于无符号整数，`PutVarint`用于有符号整数。
```go
buf := make([]byte, 10)
n := binary.PutUvarint(buf, 150)
fmt.Println(buf[:n]) // 输出: [150 1]
```
### 注意事项
- 在使用`Read`和`Write`方法时，需要确保提供的字节切片长度足够。
- 变长整数的编码和解码在处理大量数据时可以节省空间，但可能会增加编码和解码的复杂性。
### 实际应用
`encoding/binary`包通常用于网络通信、文件存储等需要处理二进制数据的场景。例如，在实现自定义协议时，可以使用这个包来编码和解码消息头或消息体。
以上是`encoding/binary`包的一些基本用法。在实际使用中，还需要根据具体的需求来选择合适的函数和方法。

在Go语言中，没有内置的方法可以自动将任意结构体转换为二进制格式。但是，你可以使用`encoding/binary`包中的函数手动实现这一功能。为了方便地将结构体转换为二进制，你通常需要确保结构体的字段是固定大小的基本数据类型，并且使用`binary`包中的`Read`和`Write`方法。
下面是一个示例，展示如何定义一个结构体并将其转换为二进制数据：

```go
package main
import (
	"bytes"
	"encoding/binary"
	"fmt"
	"io"
)
// 定义一个简单的结构体
type MyStruct struct {
	Field1 uint32
	Field2 int16
	Field3 bool
}
// 将结构体转换为二进制数据
func structToBinary(data interface{}) ([]byte, error) {
	buf := new(bytes.Buffer)
	err := binary.Write(buf, binary.BigEndian, data)
	if err != nil {
		return nil, err
	}
	return buf.Bytes(), nil
}
// 从二进制数据解析回结构体
func binaryToStruct(data []byte, obj interface{}) error {
	buf := bytes.NewReader(data)
	return binary.Read(buf, binary.BigEndian, obj)
}
func main() {
	// 创建结构体实例
	myData := MyStruct{
		Field1: 123456,
		Field2: -123,
		Field3: true,
	}
	// 转换为二进制
	binaryData, err := structToBinary(&myData)
	if err != nil {
		fmt.Println("Error converting to binary:", err)
		return
	}
	fmt.Println("Binary data:", binaryData)
	// 解析回结构体
	var myDataCopy MyStruct
	err = binaryToStruct(binaryData, &myDataCopy)
	if err != nil {
		fmt.Println("Error converting from binary:", err)
		return
	}
	fmt.Printf("Parsed struct: %+v\n", myDataCopy)
}
```
在这个示例中，我们定义了一个`MyStruct`结构体，并实现了两个函数`structToBinary`和`binaryToStruct`来处理结构体与二进制数据之间的转换。我们使用`binary.Write`来将结构体数据写入一个`bytes.Buffer`，然后将其转换为字节切片。同样，我们使用`binary.Read`来从字节切片中读取数据并填充到结构体中。
请注意，为了使这个示例工作，结构体的所有字段都必须是导出的（即首字母大写），因为`binary.Write`和`binary.Read`函数需要访问这些字段。此外，结构体中的字段类型应该是`binary`包能够处理的类型。
如果你想要更高级的序列化功能，比如支持更复杂的数据结构或自动处理字段标签，你可能需要使用像`protobuf`、`msgpack`或其他序列化库。这些库提供了更复杂的序列化机制，但需要你定义数据结构的方式符合它们的规范。