# 基础

## 函数式编程

### 入门

1. 方法本身直接赋值给某个变量，而这个变量直接就可以发起调用

+ ```GO
  func Func(a, b int) string {
  	return strconv.Itoa(a + b)
  }
  
  func main() {
  	myFunc := Func
  	str := myFunc(1, 2)
  	_ = myFunc(1, 2)
  	println(str)
  }
  ```

2. 局部方法：可以在方法内部直接声明一个局部方法，它的作用域就在本方法内

+ ```go
  func FuncLocal() {
  	fn := func(a, b int) string {
  		return strconv.Itoa(a + b)
  	}
  	println(fn(1, 2))
  }
  ```

3. 方法作为返回值

+ ```go
  func FuncReturn() func(a, b int) string {
  	return func(a, b int) string {
  		return strconv.Itoa(a + b)
  	}
  }
  
  func main() {
  	fn := FuncReturn()
  	str := fn(1, 2)
  	println(str)
  }
  ```

4. 匿名方法直接发起调用

+ ```go
  func FuncDirect() {
  	fn := func(a, b int) string {
  		return strconv.Itoa(a + b)
  	}(1, 2)
  	println(fn)
  }
  ```

### 闭包

+ **定义**：方法+它绑定的（运行）上下文

+ ```go
  func Closure(name string) func() string {
  	// return的这个方法，就叫闭包
  	// 它由方法 和 运行时的上下文（这里只有name）两部分组成
  	return func() string {
          world := "world"   // 局部变量，不属于上下文
  		return "hello, " + name + world
  	}
  }
  
  func ClosureInvoke() {
  	c := Closure("harmonic")
  	println(c())
  }
  
  func main() {
  	ClosureInvoke()
  }
  ```

+ 上下文的判定：返回的那个函数外部定义的变量才是上下文，该函数的局部变量不是上下文
+ **注意**：闭包如果使用不当可能会引起内存泄露。因为一个对象被闭包引用的话，闭包用完之前，它是不会被垃圾回收的。
+ 闭包可以捕获外部变量的引用，并在闭包内部修改它们。因此在闭包内部修改了外部变量，这个修改在闭包外部是可见的。
+ ```go
  func main() {
  	x := 10
  	func() {
  		x = 20
  	}()
  	println(x)  // 20
  }
  ```



## defer

### defer与闭包

+ 确定值原则

+ ```go
  // 作为闭包引入的：执行defer对应的方法时才确定
  func DeferClosure() {
  	i := 0
  	defer func() {
  		println(i)   // 1
  	}()
  	i = 1
  }
  
  // 作为参数引入时：定义defer得时候就确定了（因为定义的时候，参数就已经触发复制，成为该函数内的局部变量了）
  func DeferClosureV1() {
  	i := 0
  	defer func(i int) {
  		println(i)   // 0
  	}(i)
  	i = 1
  }
  ```

### defer与返回值

+ 如果想在defer里修改返回值，那么这个返回值必须要带名字，否则不能修改。

  > 第一周课件，p49

## 不要对迭代参数取地址！

+ ```go
  type User struct {
  	Name string
  }
  
  func LoopBug() {
  	users := []User{
  		{
  			Name: "Tom",
  		},
  		{
  			Name: "Jerry",
  		},
  	}
  
  	m := make(map[string]*User)
  	// 不要对迭代参数取地址！
      // 因为迭代参数的地址在内存中是同一个。
      // 遍历第一个会把第一个值放入该地址，第二个会把第二个值放入该地址...，但是该地址是不变的
  	for _, u := range users {
  		m[u.Name] = &u  
  	}
  	for k, v := range m {
  		fmt.Printf("name: %s, user: %v", k, v)  // 因此这里打印的v永远是最后一个遍历的值
  		println()
  	}
  }
  // name: Tom, user: &{Jerry}
  // name: Jerry, user: &{Jerry}
  ```

  

# Gin

+ 一个 Web 服务器被抽象成为 Engine
  + Engine 承担了路由注册、接入 middleware的核心职责。
  + 它组合了 RouterGroup，RouterGroup 才是实现路由功能的核心组件。

## gin.Context

+ 核心职责
  + 处理请求
  + 返回响应

## 路由

+ ![image-20231123153816663](PrimaryGo.assets/image-20231123153816663.png)
+ ![image-20231123153824399](PrimaryGo.assets/image-20231123153824399.png)
+ ![image-20231123160833436](PrimaryGo.assets/image-20231123160833436.png)

## 集中注册 vs 分散注册

+ ![image-20231123164937594](PrimaryGo.assets/image-20231123164937594.png)

## Bind: 接收请求数据

+ ![image-20231123201103094](PrimaryGo.assets/image-20231123201103094.png)



# DDD设计理念

+ 

# webook

## 目录结构

### v1

+ ![image-20231123170101800](PrimaryGo.assets/image-20231123170101800.png)

## 跨域请求

+ ![image-20231123213446465](PrimaryGo.assets/image-20231123213446465.png)

### preflight请求特征

+ ![image-20231123215140986](PrimaryGo.assets/image-20231123215140986.png)

### cors各个属性的含义介绍 

+ ![image-20231124145307509](PrimaryGo.assets/image-20231124145307509.png)

> 2.3：15分钟左右

## 跨域问题要点

+ ![image-20231124150716845](PrimaryGo.assets/image-20231124150716845.png)

## middleware

+ ![image-20231124144542635](PrimaryGo.assets/image-20231124144542635.png)

### 常见AOP

+ 日志、链路追踪、普罗米修斯、登录校验、鉴权。。。

### 在gin中的常见用法

+ ![image-20231124145016424](PrimaryGo.assets/image-20231124145016424.png)
+ ![image-20231124145033054](PrimaryGo.assets/image-20231124145033054.png)

## Service - Repostiory - DAO三层结构

+ ![image-20231128192634104](PrimaryGo.assets/image-20231128192634104.png)

+ ![image-20231128192547044](PrimaryGo.assets/image-20231128192547044.png)

### 调用流程

+ ![image-20231128192700252](PrimaryGo.assets/image-20231128192700252.png)

> 注意2.5中讲到的关于项目结构及其设计思想的知识
>
> + 哪怕不同层次方法的命名也很有讲究，体现了对应层次性质的理解

### 为什么domain中有User、到了dao还会重新定义一个User

+ ![image-20231128210556066](PrimaryGo.assets/image-20231128210556066.png)

## 获得邮件冲突错误（由数据库DAO层返回）

+ ![image-20231128225651519](PrimaryGo.assets/image-20231128225651519.png)
+ 其它的数据库，看看有没有暴露类似`mysql.MySQLError`的方法

### 重点：使用别名机制层层传导错误，避免跨层依赖问题

+ ![image-20231128231337097](PrimaryGo.assets/image-20231128231337097.png)

## 登录功能

### HTTP/Cookie/Session

+ ![image-20231129103833595](PrimaryGo.assets/image-20231129103833595.png)
+ ![image-20231129103846830](PrimaryGo.assets/image-20231129103846830.png)
+ ![image-20231129103856127](PrimaryGo.assets/image-20231129103856127.png)
+ ![image-20231129103905086](PrimaryGo.assets/image-20231129103905086.png)

## Gin session存储实现

+ ![image-20231204093340865](PrimaryGo.assets/image-20231204093340865.png)

## JWT校验

+ ![image-20231204193613047](PrimaryGo.assets/image-20231204193613047.png)
+ ![image-20231204193630287](PrimaryGo.assets/image-20231204193630287.png)

### 本项目约定

+ ![image-20231204213247978](PrimaryGo.assets/image-20231204213247978.png)

### 本项目接入JWT步骤

+ ![image-20231204224123265](PrimaryGo.assets/image-20231204224123265.png)

### JWT与Session的对比

+ ![image-20231204224657979](PrimaryGo.assets/image-20231204224657979.png)
+ ![image-20231204224716862](PrimaryGo.assets/image-20231204224716862.png)

## 限流

> + 手写限流算法？：`webook/pkg/ginx/middleware/ratelimit` 

### 用 Redis 做限流

+ ![image-20231205102956574](PrimaryGo.assets/image-20231205102956574.png)

## 增强登录安全性

### 通常：使用浏览器指纹

+ 浏览器指纹是一种通过收集浏览器特征信息来识别和跟踪用户的技术。这些特征信息包括浏览器类型、操作系统、屏幕分辨率、语言、插件、字体、时区等。通过对这些信息进行组合，可以生成一个唯一的浏览器指纹（hash值），从而识别和跟踪用户的行为。

### 本项目：使用 User-Agent

+ ![image-20231205105923588](PrimaryGo.assets/image-20231205105923588.png)

## k8s

### Deployment.yaml 和 Service.yaml 文档中各个字段的含义

> 第三周课件（下）P16-23

### Deployment Pod Service 三者之间的关系

+ ![image-20231205225815392](PrimaryGo.assets/image-20231205225815392.png)

> `https://chat.openai.com/share/b8bfb4ef-89fb-4d84-a5f0-72030d0e45cf` 最后两个QA

### PersistentVolume 和 PersistentVolumeClaim 的区别

+ ![image-20231206093519762](PrimaryGo.assets/image-20231206093519762.png)

### port nodeport targetport 之间的区别

+ ![image-20231206101732653](PrimaryGo.assets/image-20231206101732653.png)

## windows使用wrk压测工具

+ ![image-20231212183423575](PrimaryGo.assets/image-20231212183423575.png)
+ 在wsl中安装linux子系统，子系统中安装并使用wrk

## 使用Redis优化性能（数据库向）

### 使用序列化和反序列化

+ ![image-20231218110918965](PrimaryGo.assets/image-20231218110918965.png)

> 重要：性能优化的代码详解 4.2  20分钟左右往后
>

### Redis 存储方式简介

+ ![image-20231218114757784](PrimaryGo.assets/image-20231218114757784.png)

## 从需求和系统角度分析验证码登录的功能

+ 重点体会可扩展性的系统设计

> 第四周第二个ppt

## 依赖注入

### 什么是依赖注入

+ ![image-20231223170312801](PrimaryGo.assets/image-20231223170312801.png)

### 为什么要依赖注入

+ ![image-20231223170333411](PrimaryGo.assets/image-20231223170333411.png)

## IoC：控制反转

+ ![image-20231225092117274](PrimaryGo.assets/image-20231225092117274.png)
+ 为什么叫控制反转：
  + 强耦合情况下（UserHandler创建userService），userService控制住了UserHandler，因此控制反转就是希望UserHandler能够控制userService
  + 包括依赖发现和依赖注入，但是Go语言中不支持依赖发现，只能通过插件实现，因此更倾向于依赖注入

## 项目依赖关系

+ ![image-20231225093259115](PrimaryGo.assets/image-20231225093259115.png)

## 为什么用wire进行初始化

+ ![image-20231225103859913](PrimaryGo.assets/image-20231225103859913.png)
+ ![image-20231225104144811](PrimaryGo.assets/image-20231225104144811.png)

## 什么是面向接口编程

+ ![image-20231225104726157](PrimaryGo.assets/image-20231225104726157.png)

## 测试

### 单元测试

+ ![image-20240108144310203](PrimaryGo.assets/image-20240108144310203.png)

### 命令行

+ ![image-20240108145857900](PrimaryGo.assets/image-20240108145857900.png)

### Table Driven 模型

+ ![image-20240108150319194](PrimaryGo.assets/image-20240108150319194.png)

### 常见测试用例的定义

+ ![image-20240108150635079](PrimaryGo.assets/image-20240108150635079.png)

### Mock

+ ![image-20240108162453026](PrimaryGo.assets/image-20240108162453026.png)
+ ![image-20240108170749002](PrimaryGo.assets/image-20240108170749002.png)

### 测试流程(handler为例)

+ ![image-20240109134537920](PrimaryGo.assets/image-20240109134537920.png)

### 步骤(handler为例)

+ ![image-20240109134715566](PrimaryGo.assets/image-20240109134715566.png)

### SqlMock

+ ![image-20240110215536553](PrimaryGo.assets/image-20240110215536553.png)
+ ![image-20240110215607537](PrimaryGo.assets/image-20240110215607537.png)
+ ![image-20240110215624303](PrimaryGo.assets/image-20240110215624303.png)

### 集成测试

+ ![image-20240114101956411](PrimaryGo.assets/image-20240114101956411.png)
+ ![image-20240114131747634](PrimaryGo.assets/image-20240114131747634.png)

## 第三方服务治理

+ ![image-20240114143657470](PrimaryGo.assets/image-20240114143657470.png)

### 限流

+ ![image-20240114143742412](PrimaryGo.assets/image-20240114143742412.png)

### 自动切换不同服务商

+ ![image-20240114211613760](PrimaryGo.assets/image-20240114211613760.png)
+ ![image-20240114211625021](PrimaryGo.assets/image-20240114211625021.png)
+ ![image-20240114211658166](PrimaryGo.assets/image-20240114211658166.png)

## 装饰器模式

+ ![image-20240114160227794](PrimaryGo.assets/image-20240114160227794.png)
+ ![image-20240114160239145](PrimaryGo.assets/image-20240114160239145.png)

### 以短信服务举例

+ ![image-20240114160358003](PrimaryGo.assets/image-20240114160358003.png)

### 两种实现方法和比较

+ ![image-20240114160423955](PrimaryGo.assets/image-20240114160423955.png)

### 开闭原则、非侵入式

+ ![image-20240114160510239](PrimaryGo.assets/image-20240114160510239.png)















# 面试要点

## week1

+ ![image-20231120161931752](PrimaryGo.assets/image-20231120161931752.png)

+ defer实现机制与切片的扩容


> 第一周课件：P105-P113

## week2

+ ![image-20231129145633668](PrimaryGo.assets/image-20231129145633668.png)

### 跨域问题与解决

+ ![image-20231124151439558](PrimaryGo.assets/image-20231124151439558.png)

### 为什么使用自增主键？

+ 树单向增长，不存在页分裂
+ 适合范围查询，充分利用操作系统预读机制

> 2.6视频8：30左右

### GORM增强扩展功能（课程中写到就抽取整理出来）

+ ![image-20231129144556742](PrimaryGo.assets/image-20231129144556742.png)

## week3

+ ![image-20231205111421697](PrimaryGo.assets/image-20231205111421697.png)
+ ![image-20231206192413846](PrimaryGo.assets/image-20231206192413846.png)

## week4

### 如何在测试中维护登录态

+ ![image-20231219084355880](PrimaryGo.assets/image-20231219084355880.png)

### Redis 面试题目

+ ![image-20231219084446702](PrimaryGo.assets/image-20231219084446702.png)

### 依赖注入

+ ![image-20231225155224439](PrimaryGo.assets/image-20231225155224439.png)

## week5

### 维护一个高可用的短信服务

+ ![image-20240114224501211](PrimaryGo.assets/image-20240114224501211.png)
