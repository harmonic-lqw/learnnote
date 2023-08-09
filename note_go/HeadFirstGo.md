# 入门

## go程序目录结构

![image-20230330110256162](HeadFirstGo.assets/image-20230330110256162.png)

## 学习方向

+ 区块链、服务器/游戏服务器开发 、分布式/云计算软件工程师 

## 学习一门新技术的方法

+ ![image-20230330162317715](HeadFirstGo.assets/image-20230330162317715.png)

## 代码

+ 从终端直接运行代码，必须使用特殊的包main

## 新版和旧版包管理

+ 旧版需要把项目放到GOPATH中，第三方包和自己的包的源码都在src下

+ 新版在根目录生成go.mod文件，包管理通过此文件进行管理
  + 导入的外部包会go.mod中以`require 《包名》 《版本号》`出现
  + ![image-20230330183302824](HeadFirstGo.assets/image-20230330183302824.png)
  + ![image-20230330183327451](HeadFirstGo.assets/image-20230330183327451.png)
  + ![image-20230330183133923](HeadFirstGo.assets/image-20230330183133923.png)
  + ![image-20230330183201499](HeadFirstGo.assets/image-20230330183201499.png)
  + ![image-20230330183236615](HeadFirstGo.assets/image-20230330183236615.png)

+ `$GOPATH/src`外的源码包不能使用`go install`命令

+ **go build与go install的区别**：

  + 通过`go build`加上要编译的Go源文件名，我们即可**在当前文件夹下**得到一个可执行文件，默认情况下这个文件的名字为源文件名字去掉.go后缀

  + 与build命令相比，install命令在编译源码后还会将可执行文件或库文件安装到约定的目录下
    + go install编译出的可执行文件以其所在目录名(DIR)命名

    + go install将可执行文件安装到与src同级别的bin目录下，bin目录由go install自动创建

    + go install将可执行文件依赖的各种package编译后，放在与src同级别的pkg目录下

+ **文件权限**

  + `0: 没有权限`，`1: 执行`，`2: 写`，`3: 写、执行`，`4: 读`，`5: 读、执行`，`6: 读、写`，`7: 读、写、执行`

  ## 包名和文件夹名

  + 一个文件夹下只能有一个包，即该文件夹下的`.go`文件的第一行代码，`package 包名`都相同
    + 一个包下的`.go`文件的组织方式无所谓，可以把代码都写在一个文件下，也可以拆分成多个文件，它们都是同一个包的一部分
  + 如果有子目录，那么子目录和父目录是完全两个不同的包
  + 一个package的文件不能在多个文件夹下，如果多个文件夹下有重名package，它们是彼此无关的两个包
  + 用go run 运行的时候只能识别main作为包运行入口，package main表示一个可独立执行的程序，其他的package是不可以独立运行的
    + 每个 Go 应用程序都包含一个名为 main 的包，且里面有一个main函数作为应用程序入口
    + 可以直接运行的`.go`文件的package名必须为main且必须有main函数
  + **如何理解文件夹名和包名不一定相同**
    + ![image-20230331102616041](HeadFirstGo.assets/image-20230331102616041.png)
    + 代码中`import`的是包的导入路径、使用的是包名
    + Go语言不要求包名与导入路径有任何关系，但是按照惯例，导入路径的最后一段用作包名 => 包名应与其所在文件夹名相同
    + 因此：包名应该与保存它的目录名相匹配，但是main包是一个例外


# 基础语法

+ **变量和包**：定义和导入后就必须使用，不用就会被视为bug报错
+ **变量、函数、类型、常量的命名规则**：字母开头、任意长度、开头首字母大写表示可以在其他包中调用、开头首字母小写表示只能在当前包中使用
+ **Go的方法**：与给定类型的值相关联的函数（Go方法有点类似其他语言中附加到“对象”上的方法）
+ **变量的作用域**：由其定义的位置所在块和嵌套在该块中的任何块组成；通常用`{}`包围
  + `if`条件语句也是一个块，所以在`if`中定义的变量在`if`外不能使用
  + `if-else`分别表示两个块
  + `for`循环中的初始化语句和条件语句以及循环块都被认为在一个循环块的作用域中，因此初始化语句中声明的变量，可以在另一个循环语句中再次被声明
+ **短变量声明中**：只要有一个变量是新的，那么已经存在的变量名会被视为赋值操作
+ **函数调用**：
  + 调用当前包中定义的函数，不应该指定包名，否则会导致编译错误
+ **函数体结构**：
  + 参数必须声明类型
  + 有返回值必须声明返回值类型、有返回值类型必须有返回值、返回值类型和返回值必须匹配且一一对应
  + **返回值的声明**：可以同时声明多个返回值，用小括号括起来；声明时可以类似参数声明，为每一个返回值命名；返回值命名主要是为了方便程序员阅读，但是也属于函数块，可以不用，用的话也不需要再在函数中声明，直接赋值即可；
  + 有返回值的函数最后一行必须是`return`语句，否则会导致编译错误
+ **指针**：指向某变量的地址的值
+ **支持一级函数**：可以将函数分配给变量，然后从这些变量调用函数

  + ```go
    func sayHi() {
        fmt.Println("Hi")
    }
    func divide(a int, b int) float64 {
        return float64(a) / float64(b)
    }
    
    func main() {
        var greeterFunction func()
        var mathFunction func(int, int) float64
        greeterFunction = sayHi   // 带括号表示调用，所以这里不需要括号
        mathFunction = divide
        greeterFunction()
        fmt.Println(mathFunction(5, 2))
    }
    ```


# 常用方法

+ **错误值**
  + `err := errors.New("")`返回一个新的错误值`err`，调用`err.Error()`返回该值的错误信息
  + `err := fmt.Errorf("", value)`可以返回一个格式化的错误值
+ **包**：部分代码在多个程序之间共享

# 注释

+ **阅读包文档**：`go doc <包名> <函数名>`
+ **为包和函数添加注释**：直接在`package`和函数声明之前添加`//`注释

# 数组

+ 长度固定不可改变、存储类型一致、自动初始化为各类型的零值

+ **定义**`var myArray [3]string`

  + 字面量：`var myArray [3]string = [3]string{"1", "2"}`

  + 短变量声明/字面量：`myArray := [3]string{"1"}`，大括号可以为空，没有初值赋给相应位置

+ 调用：`myArray[0/1/2]`

+ 最常用的遍历方法：

  + ```go
    for index, value := range myArray {
        // code
    }
    ```

# 切片

+ Go的一个数据结构，可以增加更多的值，很像Python中的切片
+ **定义**`var myArray []string`
  + 声明切片变量并不会创建一个切片，还需要调用内置的make函数进行创建：`myArray = make([]sting, <初始化长度>)`
  + 短变量声明：`myArray := make([]int, <初始化长度>)`
  + 字面量：`var myArray []string = []string{"1", "2", "3"}`
  + 短变量声明/字面量：`myArray := []string{"1", "2"}`，大括号可以为空，创建一个空切片
+ 调用：`myArray[0/1/2]`
+ **性质**
  + 切片并不会自己保存任何数据，它仅仅是底层数组的元素的试图
  + 一个底层数组可以对应多个切片，这些切片可以重叠
  + 对一个底层数组修改或对任何一个切片的修改都会传播到底层数组和其他切片
  + 因此使用make或切片字面量来创建切片，而不是创建一个数组，再用一个切片在上面操作，这样就不用关心底层数组了

+ **append函数**
  + `slice = append(slice, <new_item>, ....)`在切片末尾添加元素
  + 需要确保传入的切片和返回的切片应该是一样的
    + 底层数组是不能增长的，如果数组没有足够的空间保存新元素，所有元素将会被拷贝到一个更大的数组，而且切片会被更新为引用这个新的数组
    + 因此如果传入和返回的切片不一样，就无法知道两个切片是否具有相同的底层数组，这会导致切片中的一些不一致行为

+ 切片变量有零值nil，但是可以像对待空切片一样对待nil切片


# 可变长参数函数

+ **声明时**在最后一个参数之前增加省略号：这个参数可以以切片的形式接受一组参数

  + ```go
    func myFunc(param1 int, param2 ...string) {
        //code
    }
    ```

+ **调用时**可以传递同一个类型的任意数量的值，或者传递一个切片，但传递切片时要在切片后加省略号

  + ```go
    myFunc(number, mySlice...)
    ```

# 映射

+ 一个映射是通过键来访问每一个值的集合

+ 值相同类型、键相同类型、键和值不必相同

+ **定义**：声明一个映射变量：`var myMap map[<键类型>]<值类型>`

  + 真正创建一个映射：`myMap = make(map[<键类型>]<值类型>)`
  + 短变量声明：`myMap := make(map[<键类型>]<值类型>)`
  + 字面量：`var myMap map[string]float64 = map[string]float64{"a": 1.2, "b": 5.6}`
  + 短变量声明/字面量：`myMap := map[string]float64{"a": 1.2, "b": 5.6}`，大括号可以为空，创建一个空映射

+ 几乎可以使用任意类型作为映射的键，只要这个类型可以使用==来比较

+ 访问没有赋值过的键，会得到值类型对应的零值

+ 映射变量有零值nil，当只声明没有创建的时候，它就为nil，因此不能用来赋值

+ 访问键是可选的获取第二个bool类型返回值，true表示赋过值，false表示没赋过，这是为了区分开初始化得到的零值和主动赋的零值

+ **删除**`delete(映射, 键)`

+ **遍历**

  + ```go
    for key, value := range myMap {
        //遍历键和值
    }
    for key := range myMap {
        //遍历键
    }
    for _, value := range myMap {
        //遍历值
    }
    ```


# 结构体

+ **定义**

  + ```go
    // 结构体变量定义
    var myStruct struct {
    	field1 string
    	field2 int
    }
    // 类型定义：允许创建一个新类型，每一个类型基于一个定义值如何存储的基础类型，可以使用任何类型作为基础类型，struct最常用
    type myType struct {
        // fields here
    }
    ```

+ 定义的类型和变量一样，有作用域，因此通常定义在包级别

+ 定义的类型和基本类型（struct也是基本类型的一种）一样，可以用作函数参数和返回值类型

+ 点运算符允许通过struct的指针直接访问字段

  + ```go
    func main() {
        var value myStruct
        value.myField = 3
        var pointer *myStruct = &value
        fmt.Println(pointer.myField) //pointer虽然是struct定义类型的指针，但是仍然可以使用点运算符直接访问字段值
        fmt.Println((*pointer).myField) //或者使用*取指针指向变量的值，但是一定要加括号，不然相当于视myField为指针并取值
        pointer.myField = 9 //也可以通过指针来给字段赋值
    }
    ```

+ 字面量：`myCar := car{name: "Corvette", topSpeed: "337"}`，忽略的字段被设置为各自类型的零值
+ **嵌入struct**：一个（外部）struct类型内还有一个（内部）struct类型，此时可以不定义内部struct类型名字，通过外部struct访问内部struct时，类型名当作字段名使用，或者直接忽略字段名（就像访问外部struct字段一样访问嵌入的内部struct字段）

# 定义方法

+ ```go
  type MyType string
  // 通常会说sayHi定义在了MyType类型上
  func (m MyType) sayHi() {
      fmt.Println("Hi, from", m)
  }
  
  value := MyType("Value")
  value.sayHi() // value：接收器；(m MyType)：接收器
  ```

+ 一旦方法被定义在了某个类型上，它就能被该类型的任何值调用

+ 对`int`等全局类型定义方法会导致编译错误

+ `(m MyType)`定义一个接收器参数

  + 接收器参数的类型是与之联系的方法的类型，除此之外，与其他函数参数一样
  + 接收器参数的名称一般定义为类型名称的第一个字母的小写
  + Go使用接收器参数来代替其他语言中的`self`或者`this`，后者是隐式的，而接收器参数是显式声明的
  + 方法和类型必须定义在同一个包中

  + 方法中接收器参数类型可以为指针类型，且以值类型接收器进行调用，反过来同样可以，方法会自动对接收器是指针类型还是值类型进行转换
  + 值类型和指针类型的接收器，尽量不要在一个包中混用

# 封装和嵌入

## 封装

+ **定义**：将程序中的数据隐藏在一部分代码中，而对另一部分不可见的方法称为封装

+ ```go
  package chapter10
  
  import "errors"
  
  type Date struct {
  	year  int
  	month int
  	day   int
  }
  
  func (d *Date) SetYear(year int) error {
  	if year < 1 {
  		return errors.New("invalid year")
  	}
  	d.year = year
  }
  
  func (d *Date) Year() int {
  	return d.year
  }
  ```

+ 将包的中的类型、方法设置为可导出的，类型的字段设置为不可导出的
  + 未导出的变量、struct字段、函数、方法等仍然能够被相同包的导出的函数或者方法访问
+ 以方法的方式对字段的值进行访问
  + **setter方法**：用来设置字段或者基础类型中的其他值的方法
  + **getter方法**：获取值
  + struct字段的值应该是不可导出的，为了防止用户绕过setter方法中的数据校验，直接赋值无效数据

## 嵌入

+ **定义**：一个类型使用匿名字段的方式保存到另一个struct类型中，被称为嵌入了struct
+ 嵌入类型的（可导出的）方法会被提升到外部类型，它们可以被外部类型直接调用，就像它们是在外部类型上定义的一样

# 接口

+ **定义**：在Go中，一个接口被定义为特定值预期具有的一组方法。

  + ```go
    // 可以把接口看作需要类型实现的一组行为
    type myInterface interface {
        methodWithoutParameters()
        methodWithParameter(float64)
        methodWithReturnValue() string
    }
    ```

+ **某个类型满足接口**：拥有接口定义的所有方法的类型被称做满足那个接口
  
  + 满足接口的类型可以用在任何需要接口的地方
  + 类型必须定义了所有接口声明的方法，不能缺少，但允许有其他接口未声明的方法
  + 类型定义接口声明的方法时，不仅方法名要相同，参数和返回值也要保持一致
  
+ **具体类型与接口类型**

  + **具体类型**不仅定义了值可以做什么，还定义了值是什么（定义了保存值的数据的基础类型）
  + **接口类型**不关心值是如何存储的，仅仅描述了这个值能做什么
  + 接口类型定义了方法，具体类型应该实现所有这些方法才算，这个类型满足这个接口。且这个过程是自动的，不再需要额外声明。

+ 定义了接口类型，声明了接口类型的变量，**具体类型如果满足接口，就可以把具体类型的值赋给接口类型的变量**，此变量只能调用接口类型定义的方法。

  + ```go
    package main
    
    import "fmt"
    
    type VoiseMaker interface {
    	MakeSound()
    }
    
    type Whistle string
    
    func (w Whistle) MakeSound() {
    	fmt.Println("Tweet!")
    }
    
    type Horn string
    
    func (h Horn) MakeSound() {
    	fmt.Println("Honk!")
    }
    
    func main() {
    	var toy VoiseMaker
    	toy = Whistle("Toyco Canary")
    	toy.MakeSound()
    	toy = Horn("Toyco Blaster")
    	toy.MakeSound()
    }
    ```


+ 当具体类型的方法是定义在指针上时，将具体类型的值赋给接口类型变量时，应该把值的指针赋给它，不能直接使用值
  + 因为我的接口变量要调用这个定义在指针上的方法，所以它理应是一个指针，而不是值

+ **类型断言**：当将一个具体类型的值赋给一个接口类型的变量时，类型断言可以取回具体类型

  + ```go
    type Robot string
    func (r Robot) MakeSound() {
        fmt.Println("Beep Beep")
    }
    func (r Robot) Walk() {
        fmt.Println("Powering legs")
    }
    
    type NoiseMaker interface {
        MakeSound()
    }
    func main() {
        var noiseMaker NoiseMaker = Robot("Botco Ambler")
        noiseMaker.MakeSound()
        noiseMaker.Walk() //编译错误，不能调用接口外方法
        var robot Robot, ok = noiseMaker.(Robot)  // 类型断言；ok用来判断断言是否成功；断言失败，是一个运行时错误
        robot.Walk()
    }
    ```

+ **空接口**

  + 一个不需要任何方法的接口称为空接口

  + ```go
    type Anything interface {
    }
    ```

  + 不需要实现任何方法来满足空接口，所以所有的类型都满足它
  + **常用在**定义一个接受空接口作为参数的函数，便可以传入任何类型的值作为参数
  + 但注意，空接口类型的值无法做任何操作，因为没有任何函数可以调用，此时需要使用类型断言来获得具体类型的值

# 错误处理

+ **延迟函数调用**：`defer`关键字用于函数和方法调用前，使此函数或方法在当前函数剩余代码运行完并退出当前函数后再被调用
+ **panic函数**
  + 当程序出现`panic`时，当前函数停止运行，程序打印日志消息并崩溃
  + `panic`函数需要一个满足空接口的参数（即，任何类型），该参数将被转换为字符串（如果需要），并作为`panic`日志信息的一部分打印出来
  + 当程序法生`panic`时，`panic`输出中包含堆栈跟踪，即调用堆栈列表
    + **调用堆栈**：即在任何给定点上处于活动状态的函数调用的列表
  + 延迟调用在崩溃前完成
    + 延迟调用函数(`defer`)仍然会被执行，执行顺序与被延迟的顺序相反
  + **通常**，调用`panic`应该留给”不可能的”情况，而不是无法访问的文件、网络故障、错误的用户输入等通常被认为是“正常的”错误
+ **recover函数**
  + 如果延迟的函数调用`recover`函数，程序将从`panic`状态中恢复（如果有的话）
  + `recover`函数返回最初传递给`panic`函数的任何值
  + 正常程序执行过程中调用`recover()`只返回`nil`
+ `recover`并不会使程序出现`panic`时恢复执行，至少不会完全恢复，因为产生`panic`的函数会立即返回，该函数块中`panic`之后的代码都不会执行（这也是为什么要用`defer`去延迟调用`recover`），但在产生`panic`的函数返回后，将正常执行后面的程序
+ **在调用可能引起panic的代码之前，延迟调用调用recover函数的函数**

# 并发任务

+ **goroutine**：即并发任务

  + 在其他语言中有个类似的概念，叫做线程，但`goroutine`比现程需要更少的计算机内存、更快的启动和停止时间，意味着可以同时运行更多的`goroutine`
  + `goroutine`允许并发：暂停一个任务来处理其他任务。在某些情况下，它们允许并行：同时处理多个任务
  + **启动一个goroutine**：使用`go myFunction()`
  + 函数返回值不能在go语句中使用，最直观的一个原因是因为当调用函数试图使用返回值时，返回值还没有准备好

+ **channel**：`goroutine`之间的一种交流方式

  + `channel`允许将一个值从一个`goroutine`发送到另一个，还确保接受的`goroutine`尝试使用该值之前，该值已经发送

  + 每个`channel`只携带特定类型的值：`var myChannel chan float64`

  + ```go
    var myChannel chan float64
    myChannel = make(chan float64)
    // or
    myChannel := make(chan float64)
    ```

  + 发送和接受值

    + ```go
      func greeting(myChannel chan string) {
      	myChannel <- "hi"  //在channel上发送值：myChannel <- <value>
      }
      
      func main() {
      	myChannel := make(chan string)
          go greeting(myChannel)
          fmt.Println(<-myChannel)  //在channel上接收值：<-myChannel
      }
      ```

  + **同步**

    + `channel`通过`blocking(阻塞)`来使各个`goroutine`之间同步
    + 发送操作阻塞发送的`goroutine`，直到另一个`goroutine`在同一`channel`上执行了接收操作
    + 反之亦然：接收操作阻塞接收的`goroutine`，直到另一个`goroutine`在同一`channel`上执行了发送操作


# 自动化测试

+ **自动化测试**是一个独立的程序，它执行主程序的组件，并验证它们的行为是否符合预期
+ 每次添加一个新特性，都通过运行程序来测试它，这只能测试到新特性，如果还要所有的旧特性，以确保新的更改没有破坏任何东西，自动化测试将发挥作用
+ **自动化测试**使用一组特定的输入运行代码，并寻找特定的结果。只要代码的输出与期望值匹配，则测试将“通过”

+ 测试文件的代码由普通的Go函数组成，但需要遵循一定的约定才能使用`go test`工具

  + `go test <包名>`命令后：`-v`显示所有测试信息；`-run <模糊匹配函数名>`只测试模糊匹配到的测试函数

  + 测试代码与被测试代码可以同包也可以不同包，但如果想访问未导出类型或函数，则需要同包

  + 测试文件需要以`_test.go`结尾，测试函数需要以`Test`开头，否则会被`go test`忽略

  + 测试函数应该接收单个参数：一个指向`testing.T`值的指针

  + 通过调用`testing.T`值的一些方法（比如Error）来报告测试失败和错误信息

  + ```go
    func TestOneElement(t *testing.T) {
        list := []string("apple")
        want := "apple"
        got := JoinWithCommas(list)
        if got != want {
            t.Error(errorString(list, got, want))
        }
    }
    
    func errorString(list []string, got string, want string) string {
        return fmt.Sprintf("JoinWithCommas(%#v) = \"%s\", want \"%s\"", list, got, want)
    }
    ```

+ **测试驱动开发**：编写测试 -> 确保通过 -> 重构代码

  + 自由地修改代码，而不用担心代码被破坏是需要单元测试的真正原因
