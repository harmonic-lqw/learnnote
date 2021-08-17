## 创建项目：

+ django-admin startproject  <项目名>

## path函数：

+ ![image-20210122203653485](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20210122203653485.png)

## 模板：

+ 分析文档的表现形式和内容，使用模板来输出数据，实现数据与视图的分离

+ 1. 在项目文件夹下建立templates包，里面是html文件，表示视图如何显示形式，模板中的变量用{{ 变量名 }}表示

  2. 改变views中的响应，不是直接响应数据，而是按照模板进行响应，使用render函数

     ```python
     def runoob(request):
         context = {}
         context['hello'] = "Hello World!"
         return render(request, 'runoob.html', context)
     ```

     **context参数是一个字典，字典的键值对应模板中的变量**

  3. 项目的urls中设置路由

  4. setting.py中配置相应的TEMPLATES的位置

### 掌握常用模板的语法规则

## 模型： 

+ Django对各种数据库提供统一的调用API
+ Django规定使用模型，必须创建一个APP
+ 1. 创建数据库
  2. 在项目的settings.py中配置好数据库信息
  3. 在与settings.py同级目录下的“___init__.py“中导入pymysql，告诉Django使用pymysql模块连接mysql数据库
  4. 创建一个APP‘’
  5. 在APP中的models.py文件中创建数据表，一个类是一个表，可在类中对表的结构，字段，数据类型等进行定义，表的名字结构是‘应用名_类名’。
  6. 在settings.py中INSTALLED_APPS中添加上应用名、
  7. 命令行运行相关命令
  8. 最后可以在项目总目录里面创建一个.py文件进行数据库操作

## GET请求：

+ 工程文件中创建.py文件，里面有表单请求处理函数，对应找到表单模板，还有视图显示函数，用于返回表单提交后的相应结果
+ 模板里面创建表单模板文件，即.html文件
+ 根据表单的语法来设置表单提交方式和触发.py文件中视图显示函数的方式
+ 在项目的urls里为两个函数都配置相应的路由

## POST请求：

+ 数据类型是QueryDict，常用于form表单，form表单里的标签name属性对应参数的键，value属性对应参数的值

+ 工程文件中创建.py文件，里面有一个能够进行表单处理请求和视图显示的函数
+ 模板中创建相应的.html文件
+ 在模板中根据相关表单语法再次触发视图显示函数
+ 在项目urls中**正确**配置路由

## HttpResponse对象：

+ 响应对象主要有三种形式：HttpResponse()、render()、redirect()
+ render()：底层返回的是HttpResponse对象
+ redirect()：底层继承的是HttpResponse对象

## 路由：

+ re_path用于正则路径，需要自己手动添加正则首位限制符号
+ paht 用于普通路径
+ 正则路径分为有名分组：'(?<组名>正则表达式)'和无名分组‘（正则表达式）’，**组名要和视图函数中的形参数量、名称一致**

### 反向解析：

+ 利用反向解析，当路由层url发生改变，视图层和模板层，动态反向解析处更改后的url，免去修改操作
+ 反相解析一般用在模板中的超链接和视图中的重定向
+ 从'django.urls'中引入'reverse'，普通路径参数只有路由别名，正则无名分组，参数有'args=(符合正则匹配的参数,)'，正则有名分组，参数有'kwargs={“组名”： “符合正则匹配的参数”}'，**注意参数均为reverse的参数**
+ 在模板层的html文件中，｛% url "路由别名" 参数（与reverse参数对应，普通路径没有，正则无名分组直接是元组中的值，正则有名分组是：组名=值）｝

### 命名空间：

+ include参数中要加一个应用名称参数
+ 视图函数中的命名空间应改为：reverse（“app名称：路由别名”）
+ 模板层的html文件中，命名空间是：路由别名改为app名称：路由别名

## admin管理工具：

+ python manage.py createsuperuesr 创建超级用户
+ 修改模型里的admin.py文件，让模型的数据模型注册到admin
+ 对于复杂模型，改变模型后
  + python manage.py makemigrations <模型名>       让Django指导模型有一些变更
  + python mange.py migrate <模型名>     创建表结构
+ 可以通过在admin.py文件中定义相关用来自定义表单
+ 可以控制内联显示，来体现两个有外键关系的表，在admin中体现出两者的从属关系
+ list_display属性控制显示内容
+ search_fields属性控制搜索功能

## Django单表实例：

### 创建模型：

+ 在models.py中添加类
+ 执行:python manage.py migrate 创建表结构
+ python  manage.py makemigrations app01 让Django知道我们的模型有一些改动
+ python manage.py migrate app01 创建表结构
+ 表名为：应用名_类名

### 数据库添加：

+ 在实例化对象中直接按字段添加，之后要执行：对象.save()
+ 通过ORM提供的objects提供的方法create来实现

### 查找：

+ 使用all()方法，返回QuerySet类型数据，里面放的是模板类对象
+ filter()查询符合条件的数据，返回QuerySet类型数据，里面放的是模板类对象
+ exclude()查询不符合条件的数据，返回QuerySet类型数据，里面放的是模板类对象
+ get()查询符合条件的对象只能为一个
+ order_by()对查询结果进行排序，返回QuerySet类型数据，里面放的是模板类对象
+ reverse()用于对查询结果进行反转，返回QuerySet类型数据，里面放的是模板类对象
+ count()查询数据的数量，返回数据是整数
+ first()返回第一条数据，返回的是模板类对象
+ last()返回最后一条数据，返回的是模板类对象
+ exists()查询QuerySet列表里是否有数据，返回布尔类型数据，不能对整形和模板类对象进行判断，只能判断QuerySet列表
+ values()查询部分字段数据，返回QuerySet类型数据，里面放的是一个可迭代的字典序列，字典里的键是字段，值是数据，参数字段要加引号
+ values_list()查询部分字段数据，返回QuerySet类型数据，里面放的是元组，元祖里放的是查询字段对应的数据
+ distinct()对数据进行去重，返回QuerySet类型数据，一般联合values和values_list使用，对QuerySet进行去重，对模板类对象去重没有意义，因为每个对象都是不一样的存在

+ filter和exclude基于双下划线的模糊查询
  + 双下划线前是字段名
  + __in用于读区间，=后是列表
  + __gt大于号，=后是数字
  + __gte大于等于号，=后是数字
  + __lt小于号，=后是数字
  + __lte小于等于号，=后是数字
  + __range用与范围，=后是列表
  + __contains表示包含，=后是字符串
  + __icontains表示不区分大小写的包含，=后是字符串
  + __startswith以指定字符开头，=后是字符串
  + __endswith以指定字符结尾，=后是字符串
  + __year是DateField数据类型的年份，=后是数字
  + __month是DateField数据类型的月份，=后是数字
  + __day是DateField数据类型的天数，=后是数字

### 删除：

+ 对模板类对象，使用：对象.delete()，返回一个元组，第一个元素是受影响的行数
+ 对QuerySet的数据类型，使用：QuerySet.delete()，返回一个元组，第一个元素是受影响的行数
+ 但是删除所有元素的时候，要加all()

### 修改：

+ 使用：模板类对象.属性 = 更改后的值，但之后要写：模板类对象.save()
+ 使用：QuerySet类型数据.update(字段名=更改后的值)，返回整数，是受影响的行数

## 多表实例：

## 聚合查询：

## Django Form组件：

+ 用于对页面进行初始化，生成HTML标签，还可以对用户提交的数据进行校验
  + 显示顺序：
    + 先显示字段错误信息，再显示局部钩子错误信息
    + 若显示了字段错误信息，不会再显示局部钩子错误信息
    + 若有全局钩子，则全局钩子是等所有数据校验完，才开始进行校验，且全局钩子的错误信息一定会显示

## Django Auth组件：

+ 一般用在用户的登陆注册上，用于判断当前的用户是否合法，并跳转到登陆成功或失败的页面

## Cookie与session:

+ cookie是存储在客户端计算机上的文本文件，保留各种跟踪信息
+ 一个web服务器可以分配一个唯一的session会话ID作为每个Web客户端的cookie，对于客户端的后续请求可以使用接收到的cookie来识别
+ 使用session来完成会话跟踪，session底层依赖cookie技术

+ session是保存在服务端的键值对，服务器在运行时为每一个用户的浏览器创建一个独享的session对象，由于session对象为用户浏览器独享，用户访问服务器中web资源时，可以吧数据放在各自的session中，当用户再去访问服务器中的其他web资源时，其它web资源再从各自用户的session中取出数据为用户服务。
+ session保存在服务端，创建session后传给客户端cookie，cookie用于标识不同用户，弥补http无状态的缺陷，第二次客户端请求时，服务端通过根据cookie来判断哪个用户，并执行相应操作。

## 中间件：

### 有四个自定义中间件方法:

+ process_requset(self, request)，返回值是None，正常走流程，进入下一个中间件，返回值是HttpResponse，则以该中间件为起点倒序执行中间件。
  + 不同中间件之间传递的request参数都是同一个请求对象
+ process_response(self, request, response)，返回值必须是response
+ process_view(request, view_func, view_args, view_kwargs)
  + view_func是Django即将使用的视图函数，view_args是将传递给视图的位置参数列表，view_kwargs是将传给视图的关键字参数的字典
  + 该方法在视图函数之前，process_request方法之后执行的
  + 返回值是None，则按照正常流程走，进入下一个中间件
  + 返回值是HttpResponse对象，以该中间件为起点，倒序执行中间件
  + 返回值是view_func(request)，直接提前执行视图函数，然后再倒序执行视图函数之后执行的方法
+ process_exception(request, exception)
  + request是HttpRequest对象，exception是视图函数异常产生的Exception对象
  + 在视图函数之后，在process_response方法之前执行
  + 返回值是None，页面报错500视图函数不会执行
  + 返回值是HTTPResponse，页面不报错，视图函数不执行，中间件后续process_exception方法也不执行，直接从最后一个中间件的process_response方法倒序开始执行

## FBV与CBV：

+ FBV，基于函数的视图，就是在视图里使用函数处理请求
+ CBV，基于类的视图，在视图里使用类处理请求，此时url的配置中，要把url请求分给对应类的as_view()方法





