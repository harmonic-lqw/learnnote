[TOC]



# Django框架快速入门：



## 初识Django项目：

* 基本命令：
  * startproject：创建一个Django项目
  * startapp: 创建一个Django应用
  * check: 检验项目完整性
  * runserver: 本地简易运行Django项目
  * shell: 进入Django项目的Python Shell环境
  * test: 执行Django用例测试
* 数据库相关基本命令：
  * makemigrations: 创建模型变更的迁移文件
  * migrate: 执行上一个命令创建的迁移文件
  * dumpdata: 把数据库数据导出到文件
  * loaddata: 把文件数据导入到数据库
* 项目目录介绍:
  * 项目配置文件：settings.py
  * 项目路由配置文件：urls.py
  * 项目管理文件：manage.py

## 初识Django应用：

### 应用和项目的区别：

* 一个Django项目是一个基于Django的Web应用，是可以运行的；一个Django项目包含一组配置和若干个Django应用，是一对多的关系
* 一个Django应用是一个可重用的Python软件包，是不可以独自运行的，每个应用可以自己管理模型、视图、模板、路由、静态文件等

![image-20200826175007025](D:\note_file\note_django\images\image-20200826175007025.png)

### Django应用目录介绍

* views.py：视图处理的地方
* models.py：定义应用模型的地方
* admin.py：定义Admin模块管理对象的地方
* apps.py：声明应用的地方
* tests.py：编写应用测试用例的地方
* urls.py：（自行创建）管理应用路由的地方

## Django HelloWorld:

### 步骤：

1. 在views.py里定义hello_world函数

   ```python
   from django.http import HttpResponse
   
   
   def hello_world(request):
       return HttpResponse('hello_world')
   ```

   

2. 在应用路由层定义路径

   ```python
   from django.urls import path
   import blog.views
   
   urlpatterns = [
       path('hello_world',blog.views.hello_world)
   ]
   ```

   

3. 在项目路由层定义路径

   ```python
   from django.contrib import admin
   from django.urls import path,include
   
   urlpatterns = [
       path('admin/', admin.site.urls),
       path('blog/',include("blog.urls"))
   ]
   ```

   

4. 在设置里面开启路由

   ```python
   INSTALLED_APPS = [
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
   
       #myapp
       'blog.apps.BlogConfig'
   ]
   ```

   

### 模型：

![12](D:\note_file\note_django\images\12.png)

## Django模型层：

### 模型层：

* 位于Django视图层和数据库之间
* Python对象和数据库表之间进行转换
* 可以屏蔽不同数据库之间的差异，使开发者更加专注于业务逻辑的开发
* 提供很多便捷工具有助开发，如数据库迁移、备份等

### 模型层的配置：

​		**settings.py文件中的DATABASES**

### 创建博客文章模型：

1. 在应用层models.py中定义模型：

   ```python
   class Article(models.Model):
       # 文章的唯一ID
       article_id = models.AutoField(primary_key=True)
       # 文章标题
       title = models.TextField()
       # 文章摘要
       brief_content = models.TextField()
       # 文章内容
       content = models.TextField()
       # 发布时间
       publish_date = models.DateTimeField(auto_now=True)
   ```

2. 在Teminal中输入命令

   ```
   python manage.py makemigrations
   ```

   来创建一个模型迁移文件

3. 通过命令

   ```
   python manage.py migrate
   ```

   运行迁移文件，将迁移文件中的内容同步到数据库中

### 初识Django Shell：

* 什么是Django Shell：类似Python Shell <!--用于交互式的Python编程,不需要编译-->,继承Django项目环境，不仅可以运行Python语句，还可以使用Django里的一些对象和配置。

* 可以进行临时性操作使用Django Shell更方便测试

* 小范围的Debug更简单，而不需要运行整个项目来测试

* 方便了开发、测试、Debug

* 通过命令

  ```
  python manage.py shell
  ```

  进入Django Shell

### Django Admin模块：

* 什么是Django Admin模块：
  * 一种可以读取定义的模型元数据，并提供强大的管理使用页面的Django的后台管理工具。
  * 管理页面是基础设施中的重要部分
  * 方便实现认证用户、显示管理模型、效验输入等类似的功能

* Django Admin模块的一个简单使用：

  1. 通过命令

     ```
     python manage.py createsuperuser
     ```

     来创建一个超级用户

  2. 在admin.py文件中

     ```python
     from .models import Article
     admin.site.register(Article)
     ```

     来将定义的文章模型注册到admin模块里

  3. 通过命令

     ```
     python manage.py runserver
     ```

     来运行便可在返回的url下的 /admin 中看到用户和文章

  4. 可以通过在models.py中定义函数

     ```python
         def __str__(self):
             return self.title
     ```

     来将admin中的文章名字换成title

### 实现博客数据返回页面：

1. 在views.py文件中定义文章内容函数

   ```python
   def article_content(request):
       article = Article.objects.all()[0]
       title = article.title
       brief_content = article.brief_content
       content = article.content
       article_id = article.article_id
       publish_date = article.publish_date
       return_str = 'title: %s,brief_content: %s,content: %s,article_id: %s,publish_date: %s'%(title,brief_content,content,article_id,publish_date)
   
   
       return HttpResponse(return_str)
   ```

2. 在应用层的urls.py里配置路径

   ```python
   urlpatterns = [
       path('hello_world',blog.views.hello_world),
       path('content',blog.views.article_content)
   ]
   ```

3. 在项目层的urls.py里配置路径

   ```python
   urlpatterns = [
       path('admin/', admin.site.urls),
       path('blog/',include("blog.urls"))
   ]
   ```

   (之前配置过，这里就不用配置了)

4. 运行,便可在返回的url下的 /content 得到博客文章

## Django视图与模板：

### 使用Bootstrap实现静态博客页面：

* 什么是Bootstrap和Bootstrap栅格系统
  * 一种基于html、css、js来开发的美国Twitter的前端框架
  * 提供了非常多的控件并附带源码
  * www.bootcss.com进入中文官网
  * Bootstrap栅格系统是将页面从左到右分成了12分

* 静态文件源码：

  * 在blog下的templates（没有就新建）文件夹中定义两个.html 文件：

    1. index.html

       ```html
       <!DOCTYPE html>
       <html lang="en">
       <head>
           <meta charset="UTF-8">
           <title>Django Web框架学习</title>
           <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
           <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
       
           <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
           <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
           <!-- 这两个是从Bootstrap复制过来的核心文件-->
       </head>
       <body>
       <div class="container page-header">
           <h1>文章头部
               <small> —— 泛古玉</small>
           </h1>
       </div>
       <div class="container page-body">
       <!--    文章body-->
           <div class="col-md-9" role="main">
               <!-- 文章摘要占九份-->
               <div class="body-main">
                   <div>
                       <h2>文章标题1</h2>
                       <p>
                           段落1~~~
       
                       </p>
                       <p>
                           段落2~~~
                       </p>
                   </div>
                   <div>
                       <h2>文章标题2</h2>
                       <p>
                           段落1~~~
       
                       </p>
                       <p>
                           段落2~~~
                       </p>
                   </div>
               </div>
           </div>
           <div class="col-md-3" role="complementary">
               <!-- 文章目录占3份-->
               <div>
                   <h2>最新文章</h2>
                   <h4><a href="#">文章1</a></h4>
                   <h4><a href="#">文章2</a></h4>
                   <h4><a href="#">文章3</a></h4>
                   <h4><a href="#">文章4</a></h4>
                   <h4><a href="#">文章5</a></h4>
                   <h4><a href="#">文章6</a></h4>
                   <h4><a href="#">文章7</a></h4>
                   <h4><a href="#">文章8</a></h4>
                   <h4><a href="#">文章9</a></h4>
                   <h4><a href="#">文章10</a></h4>
               </div>
           </div>
       </div>
       
       
       </body>
       </html>
       ```

    2. detail.html

       ```html
       <!DOCTYPE html>
       <html lang="en">
       <head>
           <meta charset="UTF-8">
           <title>Django Web框架</title>
           <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
           <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
                 integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
           <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
           <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"
                   integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa"
                   crossorigin="anonymous"></script>
           <!-- 这两个是从Bootstrap复制过来的核心文件-->
       </head>
       <body>
       <div class="container page-header">
           <h1>文章标题1</h1>
       </div>
       <div class="container body-main">
           <div>
               <p>
                   段落1~~~
       
               </p>
               <p>
                   段落2~~~
               </p>
           </div>
       </div>
       
       </body>
       </html>
       ```

### Django模板：

* 什么是Django模板系统：以文本为表现形式，分析文档的表现形式和内容

* 使用模板系统的原因：因为视图文件是不适合HTML编码的，而且页面设计时如果要修改python代码，会使设计变得很复杂，而且实际开发中网页逻辑和网页视图应该分开设计

* 标签占位符：

  * 变量标签：{{变量}}

    ```html
    <p>Dear {{ person_name }},</p>
    ```

  * for循环标签：{% for x in list %},{% endfor %}

    ```html
    <ul>
        {% for item in item_list %}
        <li>{{ item }}</li>
        {% endfor %}
    </ul>
    ```

  * if-else分支标签：{% if %},{% else %},{% endif %}

    ```html
    {% if ordered_warranty %}
    <p>Your warranty information will be included in the packaging.</p>
    {% else %}
    <p>You didn't order a warranty, so you're on your own when
        the products inevitably stop working.</p>
    {% endif %}
    ```

### 使用模板系统渲染博客：

1. 先使用相应的模板替换原html文件中的内容

   改变后的detail.html

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Django Web框架</title>
       <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
       <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
             integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
       <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
       <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js"
               integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa"
               crossorigin="anonymous"></script>
       <!-- 这两个是从Bootstrap复制过来的核心文件-->
   </head>
   <body>
   <div class="container page-header">
       <h1>{{ current_article.title }}</h1>
   </div>
   <div class="container body-main">
       <div>
           {% for section in section_list %}
           <p>{{ section }}</p>
           {% endfor %}
       </div>
   </div>
   
   </body>
   </html>
   ```

   index.html

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Django Web框架学习</title>
       <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
       <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
   
       <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
       <script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
       <!-- 这两个是从Bootstrap复制过来的核心文件-->
   </head>
   <body>
   <div class="container page-header">
       <h1>文章头部
           <small> —— 泛古玉</small>
       </h1>
   </div>
   <div class="container page-body">
   <!--    文章body-->
       <div class="col-md-9" role="main">
           <!-- 文章摘要占九份-->
           <div class="body-main">
               {% for article in article_list %}
               <div>
                   <h2>{{ article.title }}</h2>
                   <p>
                       {{ article.brief_content }}
                   </p>
               </div>
               {% endfor %}
           </div>
       </div>
       <div class="col-md-3" role="complementary">
           <!-- 文章目录占3份-->
           <div>
               <h2>最新文章</h2>
               {% for article in article_list %}
               <h4><a href="#">{{ article.title }}</a></h4>
               {% endfor %}
           </div>
       </div>
   </div>
   
   
   </body>
   </html>
   ```

2. 将这两个文件移入templates中的新建的blog文件夹下

3. 再在视图文件views.py中定义相关的函数

   ```python
   def get_index_page(request):
       all_article = Article.objects.all()
       return render(request, 'blog/index.html',   # 自动搜索templates下的文件
                     {
                         'article_list' : all_article
                     }
                     )
   
   def get_detail_page(request):
       current_article = Article.objects.all()[0]
       section_list = current_article.content.split('\n')
       return render(request, 'blog/detail.html',   # 自动搜索templates下的文件
                         {
                             'current_article': current_article,
                             'section_list': section_list
                         }
                         )
   ```

   <!--其中render的作用是将模板系统和数据进行渲染并返回,第一个参数是request；第二个参数是对应的文件路径；第三个参数是要传递给.html文件的字典形式的模板变量-->

### 实现文章详情页面的跳转：

1. 设计文章详情页的URL

   ```python
   path('detail/<int:article_id>', blog.views.get_detail_page)
   ```

2. 完善视图函数逻辑

   ```python
   def get_detail_page(request, article_id):
       all_article = Article.objects.all()
       current_article = None
       for article in all_article:
           if article.article_id == article_id:
               current_article = article
               break
       section_list = current_article.content.split('\n')
       return render(request, 'blog/detail.html',   # 自动搜索templates下的文件
                         {
                             'current_article': current_article,
                             'section_list': section_list
                         }
                         )
   ```

### 实现上下篇文章跳转：

1. 从bootstrap中copy相应代码到detail.html中

   ```html
   <div>
       <nav aria-label="...">
     <ul class="pager">
       <li><a href="/blog/detail/{{ previous_article.article_id }}">上一篇：{{ previous_article.title }}</a></li>
       <li><a href="/blog/detail/{{ next_article.article_id }}">下一篇：{{ next_article.title }}</a></li>
     </ul>
   </nav>
   </div>
   ```

2. 完善视图文件

   ```python
   def get_detail_page(request, article_id):
       all_article = Article.objects.all()
       current_article = None
       previous_index = 0
       next_index = 0
       previous_article = None
       next_article = None
   
       for index, article in enumerate(all_article):
           if index == 0:
               if len(all_article) == 1:
                   previous_index = 0
                   next_index = index
               else:
                   previous_index = 0
                   next_index = index + 1
           elif index == len(all_article) - 1:
               previous_index = index - 1
               next_index = index
           else:
               previous_index = index - 1
               next_index = index + 1
   
           if article.article_id == article_id:
               current_article = article
               previous_article = all_article[previous_index]
               next_article = all_article[next_index]
               break
   
       section_list = current_article.content.split('\n')
       return render(request, 'blog/detail.html',   # 自动搜索templates下的文件
                         {
                             'current_article': current_article,
                             'section_list': section_list,
                             'previous_article': previous_article,
                             'next_article': next_article
                         }
                         )
   ```

### 实现分页功能：

1. 从Bootstrap复制相应分页代码

   ```html
   <!-- 分页-->
       <div class="body-footer">
           <div class="col-md-4 col-md-offset-3">
           <nav aria-label="Page navigation">
               <ul class="pagination">
                   <li>
                       <a href="/blog/index?page={{previous_page}}" aria-label="Previous">
                           <span aria-hidden="true">&laquo;</span>
                       </a>
                   </li>
                   {% for num in page_num %}
                   <li><a href="/blog/index?page={{num}}">{{ num }}</a></li>
                   {% endfor %}
   
                   <li>
                       <a href="/blog/index?page={{next_page}}" aria-label="Next">
                           <span aria-hidden="true">&raquo;</span>
                       </a>
                   </li>
               </ul>
           </nav>
           </div>
   ```

2. 设计分页URL

   ```python
    # 获取page
       page = request.GET.get('page')
       if page:
           page = int(page)
       else:
           page = 1
       print(page)
   ```

3. 使用Django分页组件实现分页功能（仍然在views.py文件中实现）

   ```python
   from django.core.paginator import Paginator
   
   def get_index_page(request):
       # 获取page
       page = request.GET.get('page')
       if page:
           page = int(page)
       else:
           page = 1
       print(page)
   
       all_article = Article.objects.all()
       paginator = Paginator(all_article,2)
       page_num = paginator.num_pages
       print('page num:', page_num)
       page_article_list = paginator.page(page)
       if page_article_list.has_next():
           next_page = page + 1
       else:
           next_page = page
       if page_article_list.has_previous():
           previous_page = page - 1
       else:
           previous_page = page
   
       # render: 把模板系统和数据进行渲染并返回
       return render(request, 'blog/index.html',   # 自动搜索templates下的文件
                     {
                         'article_list' : page_article_list,    # 当前页的文章
                         'page_num': range(1, page_num + 1),
                         'curr_page': page,               # 当前页
                         'next_page': next_page,              # 下一页
                         'previous_page': previous_page           # 前一页
                     }
                     )
   ```

### 实现最近5篇文章列表：

1. 在views.py文件中的get_index_page方法加入

   ```python
   top5_article_list = Article.objects.order_by("-publish_date")[:5]
   ```

   <!--这里的5可以根据需要变换；publish_date前要加个－否则会发现文章显示的是最老的5篇；---->

2. 在返回的模板数据中加入

   ```python
   'top5_article_list': top5_article_list,  # 前5篇
   ```

3. 将index.html文件中的最新文章里的article_list 转换成top5_article_list即可