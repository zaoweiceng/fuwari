---
title: go语言学习笔记（1）
published: 2024-08-10
description: 'go语言学习之路'
image: './luanpo.jpeg'
tags: [go, 学习]
category: 'go'
draft: false 
---

1. 编译器会自动选择在栈上还是在堆上分配局部变量的存储空间，这个选择并不是由用var还是new声明变量的方式决定的。因此，函数内声明的函数指针，赋值后，返回该指针，函数外仍然可用。

   - Go语言的自动垃圾收集器是如何知道一个变量是何时可以被回收呢？基本的实现思路是，从每个包级的变量和每个当前运行函数的每一个局部变量开始，通过指针或引用的访问路径遍历，是否可以找到该变量。如果不存在这样的访问路径，那么说明该变量是不可达的，也就是说它是否存在并不会影响程序后续的计算结果。因为一个变量的有效周期只取决于是否可达，因此一个循环迭代内部的局部变量的生命周期可能超出其局部作用域。同时，局部变量可能在函数返回之后依然存在。

2. go语言中指针，实现类似c++中的`*(p+1)`来访问地址

   - ```go
     func point() *int32 {
     	var i int32 = 0
     	return &i
     }
     
     func main(){
         var p = point()
     	*p = 100
     	var p1 = point()
     	var p2 = point()
     	*p2 = 200
         
         *(*int32)(unsafe.Pointer(uintptr(unsafe.Pointer(p)) + uintptr(unsafe.Sizeof(*p)))) //*(p+1) -> p1
         *(*int32)(unsafe.Pointer(uintptr(unsafe.Pointer(p)) + 2*uintptr(unsafe.Sizeof(*p))))  //*(p+2) ->p2
     }
     ```

3. go中的swap：

   ```go
   a, b = b, a
   num[0], num[1], num[2] = num[1], num[2], num[0]
   ```

4. 切片操作可能出问题：

   ```go
   var nums = []int{1, 2, 3, 4, 5}
   a := nums[1:3]
   //对a的任何操作都会反作用于nums
   //若a.append(a, 100)，那么会修改nums[3]的值。
   //
   ```

5. 匿名嵌入

   ```go
   type Point struct {
       X, Y int
   }
   
   type Circle struct {
       Center Point
       Radius int
   }
   
   type Wheel struct {
       Circle Circle
       Spokes int
   }
   var w Wheel
   //因为匿名嵌入
   w.X = 10 //等价于 w.Circle.Point.X = 10
   w.Y = 100
   //但是赋值的时候必须循环嵌套,下面这种会报错
   w = Wheel{X: 8, Y: 8, Radius: 5, Spokes: 20} // compile error: unknown fields
   ```

6. 导出结构，只有大写字母开头的成员才会被导出

   ```go
   type Movie struct {
   	Title  string `json:"标题"`
   	Year   int    `json:"年"`
   	Color  bool   `json:"color, omitempty"` //omitempty选项，表示当Go语言结构体成员为空或零值时不生成该JSON对象
   	Actors []string
   	test   string // 小写字母开头不会被编码
   }
   var movies = []Movie{
   	{Title: "Casablanca", Year: 1942, Color: false,
        Actors: []string{"Humphrey Bogart", "Ingrid Bergman", "Claude Rains"}, test: "test"}}
   
   data, err := json.Marshal(movies)
   //data的json字符串中没有test字段
   ```

   