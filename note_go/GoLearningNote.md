# 反射

+ 反射（reflect）可以让我们能在运行期间探知对象的类型信息和内存结构，这在一定程度上弥补了静态语言在动态行为上的不足。

+ Go语言的反射操作所需的全部信息都源自接口变量。接口变量除存储自身类型外，还会保存实际对象的类型数据

  + ```go
    // 这两个反射入口函数，会将任何传入的对象转换为接口类型
    func TypeOf(i interface{}) Type
    func ValueOf(i interface{}) Value
    ```

+ 反射通常会造成很大的性能损失

## 类型

+ ```go
  package main
  
  import (
  	"fmt"
  	"reflect"
  )
  
  func main() {
  	type X int
  	var a X
  	a = 100
  	t := reflect.TypeOf(a)
  	// Type表示真实类型（静态类型）；Kind表示基础结构（底层类型）类型
  	fmt.Println(t.Name(), t.Kind(), t)  //  X int main.X
  }
  ```

+ ```go
  // 除通过试集对象获取类型外，也可以直接构造一些基础复合类型
  func main() {
      a := reflect.ArrayOf(10, reflect.TypeOf(byte(0)))
      b := reflect.MapOf(reflect.TypeOf(""), reflect.TypeOf(0))
      
      fmt.Println(a, m)  // [10]uint8 map[string]int
  }
  ```

+ ```go
  // 基类型和指针类型不属于同一类型
  func main() {
  	x := 100
  	tx, tp := reflect.TypeOf(x), reflect.TypeOf(&x)
  
  	fmt.Println(tx, tp, tx == tp)  // int *int false
  	fmt.Println(tx.Kind(), tp.Kind())  // int ptr
      fmt.Println(tp.Elem())  // int
      // 方法Elem返回指针、数组、切片、字典（值）或通道的基类型（基类型!=底层类型）
  }
  ```

+ ```go
  // 只有获取指针的基类型后，才可以遍历它的字段
  type user struct {
  	name string
  	age  int
  }
  
  type manager struct {
  	user
  	title string
  }
  
  func main() {
  	var m manager
  	t := reflect.TypeOf(&m)
  
      // 如果是指针，则取出基类型（main.manager）
  	if t.Kind() == reflect.Ptr {
  		t = t.Elem()
  	}
  	for i := 0; i < t.NumField(); i++ {
  		f := t.Field(i)
          // Offset:偏移量，以字节为单位。每个字段在结构体中占据一定的字节空间，这个偏移量表示该字段相对于结构体起始位置的字节偏移量。通过这个偏移量，可以确定字段在内存中的位置。在某些情况下，需要直接访问结构体中的字段，而不是通过结构体的实例来访问。使用偏移量可以直接计算字段在内存中的地址，并对其进行操作。需要注意的是，使用偏移量进行底层内存操作是不安全的，因为它绕过了Go语言的类型系统和内存安全机制。
  		fmt.Println(f.Name, f.Type, f.Offset) 
  		if f.Anonymous {
  			for x := 0; x < f.Type.NumField(); x++ {
  				af := f.Type.Field(x)
  				fmt.Println("    ", af.Name, af.Type, af.Offset)
  			}
  		}
  	}
  }
  ```

+ ```go
  // 对于匿名字段，可以根据名称或多级索引（按定义顺序）直接访问
  func main() {
  	var m manager
  
  	t := reflect.TypeOf(m)
  
  	name, _ := t.FieldByName("name") // 按名称查找 
  	fmt.Println(name.Name, name.Type)  // name string
  
  	age := t.FieldByIndex([]int{0, 1}) // 按多级索引查找
  	fmt.Println(age.Name, age.Type)  // age int
  }
  ```

+ ```go
  // 可用反射提取struct tag， 还能自动分解。常用于ORM映射，或数据格式验证。
  type user struct {
  	name string `field:"name" type:"varchar(50)`
  	age  int    `field:"age" type:"int`
  }
  
  func main() {
  	var u user
  	t := reflect.TypeOf(u)
  	for i := 0; i < t.NumField(); i++ {
  		f := t.Field(i)
  		fmt.Printf("%s: %s %s\n", f.Name, f.Tag.Get("field"), f.Tag.Get("type"))
  	}
  }
  // name: name varchar(50)
  // age: age int
  ```

+ ```go
  // 反射能探知当前包或外包的非导出结构成员；相对reflect而言，当前包和外包都是“外包”
  import (
  	"fmt"
  	"net/http"
  	"reflect"
  )
  
  func main() {
  	var s http.Server
  	t := reflect.TypeOf(s)
  
  	for i := 0; i < t.NumField(); i++ {
  		fmt.Println(t.Field(i).Name)  // 首字母大写和小写的都会被导出
  	}
  }
  ```

+ ```go
  type X int
  
  func (X) String() string {
  	return ""
  }
  
  func main() {
  	var a X
  	t := reflect.TypeOf(a)  // t是一个 reflect.Type 类型的值，表示类型 X 的反射类型。
  
  	st := reflect.TypeOf((*fmt.Stringer)(nil)).Elem()  // st 是一个 reflect.Type 类型的值，表示 fmt.Stringer 接口类型的反射类型
  	fmt.Println(t.Implements(st))  //  t.Implements(st) 检查 t 所代表的类型是否实现了 st 所代表的接口类型
  
  	it := reflect.TypeOf(0)  // 使用 reflect.TypeOf() 函数获取类型 int 的反射类型，并将结果赋值给变量 it
      fmt.Println(t.ConvertibleTo(it))  //  t.ConvertibleTo(it) 检查 t 所代表的类型是否可以转换为 it 所代表的类型
      							   // 转换: int(a)->i(var i int)
  
  	fmt.Println(t.AssignableTo(st), t.AssignableTo(it))  // t.AssignableTo(st) 检查 t 所代表的类型是否可以赋值给 st 所代表的类型
      // 赋值: a.String()->s(var s string);需要X实现相关方法
      
      // true
      // true
      // true false
  }
  ```

## 值

+ `Type`获取类型信息；`Value`专注于对象实例数据读写

+ ```go
  // 一、接口变量会赋值对象，且是unaddressable的，所以想修改目标对象，必须使用指针。
  // 二、传入指针仍需要通过Elem获取目标对象。因为被接口存储的指针本身是不能寻址和进行设置的。
  func main() {
      a := 100
      va, vp := reflect.ValueOf(a), reflect.ValueOf(&a).Elem()
      fmt.Println(va.CanAddr(), va.CanSet())  // false false
      fmt.Println(vp.CanAddr(), vp.CanSet())  // true true
  }
  ```

+ ```go
  // 不能对非导出字段直接进行设置操作，需要使用指针，无论是当前包还是外包
  type User struct {
  	Name string
  	code int
  }
  
  func main() {
  	p := new(User)
  	fmt.Println(reflect.TypeOf(p).Kind())
  
  	v := reflect.ValueOf(p).Elem()
  
  	name := v.FieldByName("Name")
  	code := v.FieldByName("code")
  
  	fmt.Printf("name: canaddr = %v, canset = %v\n", name.CanAddr(), name.CanSet())  // true true
  	fmt.Printf("code: canaddr = %v, canset = %v\n", code.CanAddr(), code.CanSet())  // true false
  
  	if name.CanSet() {
  		name.SetString("Tom")
  	}
  
  	if code.CanAddr() {
  		*(*int)(unsafe.Pointer(code.UnsafeAddr())) = 100
  	}
  
      fmt.Printf("%+v\n", *p)  // {Name:Tom code:100}
  }
  ```


+ ```go
  // 可通过Interface方法进行类型推断和转换
  type user struct {
  	Name string
  	Age  int
  }
  
  func main() {
  	u := user{
  		"q.yuhen",
  		60,
  	}
  
  	v := reflect.ValueOf(&u)
  
  	if !v.CanInterface() {
  		fmt.Println("CanInterface: fail.")
  		return
  	}
  
  	p, ok := v.Interface().(*user)
  
  	if !ok {
  		fmt.Println("Interface: fail.")
  		return
  	}
  
  	p.Age++
  	fmt.Printf("%+v\n", u)  // {Name:q.yuhen Age:61}
  }
  ```

+ ```go
  // 接口有两种nil状态！解决方法是用IsNil判断值是否为nil
  func main() {
  	var a interface{} = nil
  	var b interface{} = (*int)(nil)
  
  	fmt.Println(a == nil)  // true
  	fmt.Println(b == nil, reflect.ValueOf(b).IsNil())  // false true
  }
  ```

+ ```go
  // Value中的某些方法未实现ok-idom或返回error， 所以得自行判断返回的是否未Zero Value
  func main() {
      v := reflect.ValueOf(struct{ name string }{})
      // 这里的FieldByName是Value的方法，与上一小节的Type的同名方法区分开，Type方法会返回一个bool值用于判断是否匹配成功
      fmt.Println(v.FieldByName("name").IsValid())  // true
      fmt.Println(v.FieldByName("xxx").IsValid())  // false
  }
  ```

## 方法

+ 动态调用方法，只需按`In`列表准备好所需参数即可

+ ```go
  type X struct{}
  
  func (X) Test(x int, y int) (int, error) {
  	return x * y, fmt.Errorf("err: %d", x+y)
  }
  
  func main() {
  	var a X
  
  	v := reflect.ValueOf(&a)
  	m := v.MethodByName("Test")
  
      // in准备方法参数
  	in := []reflect.Value{
  		reflect.ValueOf(1),
  		reflect.ValueOf(2),
  	}
  	
      // .Call(in)动态调用方法
  	out := m.Call(in)
  	for _, v := range out {
  		fmt.Println(v)
  	}
  }
  ```

+ ```go
  // 对于变参来说，用CallSlice更加方便
  type X struct{}
  
  func (X) Format(s string, a ...interface{}) string {
  	return fmt.Sprintf(s, a...)
  }
  
  func main() {
  	var a X
  
  	v := reflect.ValueOf(&a)
  	m := v.MethodByName("Format")
  	in1 := []reflect.Value{
  		reflect.ValueOf("%s = %d"),
  		reflect.ValueOf("x"),
  		reflect.ValueOf(100),
  	}
  	out1 := m.Call(in1)
  	fmt.Println(out1)  // [x = 100]
  
  	in2 := []reflect.Value{
  		reflect.ValueOf("%s = %d"),
  		reflect.ValueOf([]interface{}{"x", 100}),
  	}
  	out2 := m.CallSlice(in2)
  	fmt.Println(out2)  // [x = 100]
  }
  ```

## 通过反射赋值

+ ```go
  type Data struct {
  	X int
  }
  
  func main() {
  	var d = new(Data)
  	v1 := reflect.ValueOf(d)
  	fmt.Println(v1)  // &{0}
  	v2 := v1.Elem()
      fmt.Println(v2)  // {0}
  	f := v2.FieldByName("X")
  	fmt.Println(f)  // 0
  	f.Set(reflect.ValueOf(100))
  	fmt.Println(v1)  // &{100}
  	fmt.Println(v2)  // {100}
  	fmt.Println(f)  // 100
  }
  ```

# 内存分配

## 内存块

+ **内存分配器**将其管理的内存块分为两种
  + span：由多个地址连续的页（page）组成的大块内存
  + object：将span按特定大小切分成多个小块，每个小块可存储一个对象
  + span面向内部管理、object面向对象分配
+ **内存分配器组件**
  + cache：每个运行期工作线程都会绑定要给cache，用于无锁object分配
  + central：为所有cache提供切分好的后备span资源
  + heap：管理闲置span，需要时向操作系统申请新内存

## 内存分配和释放

+ ```go
  type mspan struct {
      next *mspan    // 双向链表
      prev *mspan
      start pageID    // 起始序号
      npages uintptr    // 页数
      freelist golinkptr    // 待分配的 object 链表
  }
  
  type mheap struct {
      free [_MaxMHeapList]mspan    // 页数在127以内的闲置span链表数组
      freelarge mspan    // 页数大于127（ >= 1MB）的大span链表
      // 每个central对应一种sizeclass
      central [_NumSizeClasses]struct {
          mcentral mcentral
      }
  }
  
  type mcentral struct {
      sizeclass int32    // 规格
      noempty mspan    // 链表：尚有空闲object的soan
      empty mspan    // 链表：没有空闲object， 或已被cache取走的span
  }
  
  type mcache struct {
      alloc [_NumSizeClasses]*mspan    // 以sizeclass为索引管理多个用于分配的span
  }
  ```

  

+ **分配流程**
  1. 计算待分配对象的规格（size class）
  2. 从cache.alloc数组找到规格相同的span
  3. 从span.freelist链表提取可用object
  4. 如span.freelist为空，从central获取新span
  5. 如central.nonempty为空，从heap.free/freelarge获取，并切分成object链表
  6. 如heap没有大小合适的闲置span，向操作系统申请新内存块
+ **释放流程**
  1. 将标记为可回收的object交还给所属span.freelist
  2. 该span被放回central，可供任意cache重新获取使用
  3. 如span已收回全部object，则将其交还给heap，以便重新切分复用
  4. 定期扫描heap里长时间闲置的span，释放其占用的内存
+ 以上不包括大对象，它直接从heap分配和回收

# 垃圾回收

## 三色标记和写屏障：让标记和用户代码并发的基本保障

+ 起初所有的对象都是白色
+ **并发标记两个步骤1：扫描**扫描找出所有可达对象，标记为灰色，放入待处理队列
+ **并发标记两个步骤2：标记**从队列提取灰色对象，将其引用对象标记为灰色放入队列，自身标记为黑色
+ 写屏障监视对象内存修改，重新标色或放回队列

+ 当完成全部扫描和标记工作后，剩余的不是白色就是黑色，分别代表待回收和活跃对象，清理操作只需要将白色对象内存收回即可

