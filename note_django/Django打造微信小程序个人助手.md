



# 小程序开发入门：

## 准备工作：

+ 注册微信小程序开发者账号，获取Appld
+ 下载微信开发者工具
+ 文件目录：
  + utils：存放工具相关代码
  + pages：存放页面相关代码，网络请求、文件操作等
  + components：存放组件
  + thirdparty：第三方库
  + resources：资源

## 项目工程目录：

+ 小程序文件类型：

  类似html、css、js

  + .wxml--负责结构
    + 使用<view>标签替代html中的<div>、<p>、<span>等标签来简化开发
    + 使用wx:if、wx:for、{{}}来分割js逻辑和页面逻辑
  + .wxss--负责表现
    + 新增尺寸单位，来适应移动端不同设备之间的差异
    + 分为全局样式和局部样式
  + .js--负责行为
  + .json--负责配置
    + 工具配置——project.config.json，对微信开发者工具进行配置
    + 项目配置——app.json，页面路径，界面表现等
    + 页面配置——<page name>.json，与项目配置类似，但是可以细分每一个不同页面

+ 全局入口、配置与样式
  + 全局入口——app.js
  + 全局配置——app.json
  + 全局样式——app.wxss

+ 全局配置：
  + pages列表配置：一个存有页面路径的列表
  + window属性配置：配置窗口背景、颜色，导航条，状态栏，标题属性等
  + tabbar配置：当小程序有多个页面的时候进行设置 
+ 页面配置：
  + 只能配置全局配置中的window配置项内容
  + 当与全局配置冲突，则会覆盖全局配置
  + 开启下拉刷新`enablePullDownRefresh`设置为true

## 小程序框架：

+ 小程序逻辑层
  + 小程序注册逻辑
    + app函数：app.js中globaldata为全局数据，onLaunch、onShow、onHide为生命周期回调函数
      + 是用来注册全局唯一的小程序，只能在app.js中调用一次，接受一个对象作为函数参数，参数为全局变量和生命周期回调函数
  + 页面注册逻辑
    + Page函数
      + 注册一个页面，接受一个对象作为函数参数，参数为全局变量和生命周期回调函数
    + data属性
      + `this.data.message`来访问
      + `this.setData({})`来修改
    + 全局数据
      + 获取全局唯一APP实例:`const app = getApp()`
      + 通过此实例获取全局数据`var data = app.globalData`
    + 页面生命周期
      + ![image-20200902194031357](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902194031357.png)
      + ![image-20200902194100020](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902194100020.png)
      + ![image-20200902194349621](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902194349621.png)

+ 小程序的视图层 

  +  数据绑定——语法：{{}}
  + 列表渲染——语法：wx:for
  + 条件渲染——语法：wx:if、wx:elif、wx:else
  + 绑定事件——需要去管理的事件处理
    + ![image-20200902200029410](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902200029410.png)
    + 事件绑定的写法以key、value的形式
    + key以bind或catch开头，然后跟上事件的类型

+ ![image-20200902200927780](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902200927780.png)

  ​										![image-20200902201027730](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200902201027730.png)

## 小程序提供的能力和常用API：

### 微信API：

+ 网路请求
  + http请求（异步，也就是说在请求的时候代码会继续往下执行，而不是原地等待请求结果）![image-20200904123323385](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200904123323385.png)
  + 上传文件![image-20200904125607748](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200904125607748.png)
  + 下载文件![ ](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904125828784.png)
  + *Task网络任务对象：异步任务提交以后，需要对任务进行操作时，使用Task对象，可用来中断任务、触发回调函数、关闭连接等
    + RequestTask
    + UploadTask
    + DownloadTask
    + SocketTask
+ 本地存储：将数据存储在本地缓存中指定的key中，数据存储生命周期跟小程序本身一致(下面四个均为异步)
  + wx.setStorage
  + wx.getStorage
  + wx.removeStorage
  + wx.clearStorage
+ 文件系统
  + 文件的增删查改![image-20200904131415607](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904131415607.png)
  + 文件夹操作（与linux比较相似）![image-20200904131451349](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904131451349.png)

### 开放能力：

+ 用户数据
  + 头像、昵称等公开信息——wx.getUserInfo()
  + openid等敏感数据
  + ![image-20200904132049631](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904132049631.png)
+ 推送消息：基于微信的通知渠道，小程序框架为开发者提供了一种可以高效触达用户的消息
+ 运营数据
  + 小程序管理后台——数据分析
  + 小程序数据助手

### 基础组件：

+ ![image-20200904134457403](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904134457403.png)
+ WeUI-WXSS![image-20200904134548852](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904134548852.png)
  + 步骤：
    1. 把weui代码从github上下载下来
    2. 把weui.wxss引进来
    3. 将我们所需要源码copy过来并根据需要进行改造

## 小程序对接Django：

### 本地开发对接：

+ 小程序只可以跟指定的域名进行网络请求
+ 小程序必须使用HTTPS发起网络请求
+ 但可以通过勾选不校验合法域名来取消以上限制

### 远端部署测试：

+ 使用git远端同步代码![image-20200904144931906](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904144931906.png)+
+ 管理依赖环境
  + 当我们需要把代码同步到远端的时候，把一些第三方库的名字写进`requirements.txt`这样一个文本文档里，然后在远端使用`pip install -r requirements.txt`进行安装

## 小程序发布流程：

![image-20200904150843006](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904150843006.png)

# Django视图层：

## Request与response对象：

### HTTP请求与Django Request对象：

+ 请求方法——Request对象的method属性
+ 客户端信息——Request对象的META属性
+ Cookies——Request对象的COOKIES属性
+ 请求参数——Request对象的QueryDict

### HTTP请求与Django Response对象：

+ 状态码——Response对象的status属性
+ 应答内容——Response对象的content属性
+ 延伸的Response子类——（JsonResponse，FileResponse）

### 天气案例：

1. 在Django框架新建一个第三方库存写一个聚合网API调用的python文件，并返回需要的数据

2. 视图层定义weather函数，根据request.method是GET还是POST来进行判断，并将结果通过JsonResponse来返回json数据

   注意配置好项目urls和应用urls

## Django url配置：

### Django处理请求的寻址逻辑：

+ 项目层面：根路由模块——urlpatterns
+ 应用层面：子路由模块——urlpatterns
+ 视图层面：匹配成功调用指定视图
+ ![image-20200904200751424](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904200751424.png)

### Django的RESTful url设计：

+ API接口的RESTful设计
  + 说明接口类型
    + /view——视图
    + /template——模板
    + /api——API
  + 说明接口版本
    + /view/v1
    + /template/v1.2
    + /api/v2.0.1
+ 增删改查的RESTful设计
  + HTTP请求中的GET、POST、PUT、DELETE
    + GET：从服务器获取资源
    + POST：在服务器新建一个资源
    + PUT：在服务器更新资源
    + DELETE：从服务器中删除资源
  + url中的过滤信息（分页、指定）![image-20200904202339705](/image-20200904202339705.png)

+ 资源路径的RESTful设计
  + 说明资源类型![image-20200904202442850](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904202442850.png)
+ ![image-20200904202517316](/C:/Users/de'l'l/AppData/Roaming/Typora/typora-user-images/image-20200904202517316.png)

## 实现功能助手功能清单：

### Django端：

1. 在工程文件里新建一个app.yaml文件，里面包括发布的APP数据以及欲开发的功能
2. 工程文件里新建立一个utils，其中有个response.py文件，封装一些想要返回的其他数据，比如状态码
3. 视图层定义menu文件，返回JsonResponse响应
4. 路由配置：为了更好的优化路由，在项目的文件中新建一个版本路由version.py，路由路径为(项目)urls->version->(应用)urls

### 小程序端：

1. 在全局配置app.json中定义相应的tabBar
2. app.js中的globalData中定义相关的全局数据
3. 新建一个名为menu的Page
4. 在menu.js中引入全局app，定义请求后台的函数
5. 从weui中grid的wxml代码到menu.wxml中，并根据需要修改相应的图片地址，数据名称等。前提是weui.wxss已经引入第三方库并在app.wxss中配置

## 实现图文消息：

### 文本信息与图文信息对比：

![image-20200905121939087](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200905121939087.png)

### 图文消息开发逻辑：

1. 首先在项目文件里新建一个resouces文件夹，再在里面新建一个images文件夹，并把图片保存在里面
2. 在视图层新建image文件写入相关代码，这里相关的文件路径可以再settings中配置
3. 配置路由信息

### Django类视图：

+ 使用类视图进行HTTP请求的逻辑分离
  + 在视图文件里新建一个ImageView的类，继承自View
  + 在类里面实现请求逻辑分离
  + 配置路由的时候要配置ImageView的原生方法as_view()

+ python的mixin模式![image-20200905135016995](C:\Users\de'l'l\AppData\Roaming\Typora\typora-user-images\image-20200905135016995.png)
+ Mixin继承通用视图：面向对象的Mixin可以将代码变成可重用的组件，将可重用的函数封装成一个类。在对象间进行调用的时候，可以降低耦合
  1. 在response里定义一个Mixin类，类方法可以是wrap_json_response
  2. 让ImageView同时继承这个类，以至于再次使用类方法的时候直接self.wrap_json_response即可
  3. 因为前后端通常是Json进行数据交互，所以让响应尽可能的为JsonResponse

# 综合实践：

## 实现天气应用：

### 实现一个天气的类视图：

1. 继承View和mixin类
2. 实现get和post等方法
3. 在小程序实现页面更新数据，取出后台数据
4. 实现下拉刷新等功能优化

## 优化图片备份应用：

### 进入页面自动加载已备份图片

1. image视图界面定义ImageListView类
2. 实现get方法

### 长按删除和取消上传

​	pass

### 上传成功自动更新小程序页面备份列表

​	pass

## 股票查询：

pass

## 星座运势：

pass

## 笑话应用：

pass

# Django会话管理（视图层）：



















