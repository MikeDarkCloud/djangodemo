## Django学习笔记（一）-安装、创建工程应用

- 学习使用版本: 2.2.17
- 参考官网教程
- 安装直接使用pip命令安装:pip install django==2.2.17

## 1、创建工程

~~~shell
django-admin startproject djangodemo
~~~

工程目录结构：

~~~shell
─djangodemo
    │  manage.py
    │  
    └─djangodemo
            settings.py
            urls.py
            wsgi.py
            __init__.py
~~~

目录结构说明：

~~~shell
djangodemo: 项目的容器，可以随意命名。
manage.py: 一个让你用各种方式管理 Django 项目的命令行工具。
djangodemo/__init__.py：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。
djangodemo/settings.py：Django 项目的配置文件。
djangodemo/urls.py：Django 项目的 URL 声明，就像你网站的“目录”。
djangodemo/wsgi.py：作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。
~~~

## 2、启动服务

~~~shell
E:\PyProject\Development\djangodemo>python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
February 01, 2021 - 19:32:15
Django version 2.2.17, using settings 'djangodemo.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
[01/Feb/2021 19:32:22] "GET / HTTP/1.1" 200 24
~~~

启动后就可以访问**http://127.0.0.1:8000/** 

![](E:\学习\django\第一篇\1.png)

##3、项目vs应用

应用是一个专门做某件事的网络应用程序（比如博客系统、投票系统）。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。 

创建应用：

~~~shell
python manage.py startapp polls
目录结构如下：
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
~~~

##4、编写第一个视图

在新建的polls应用的views.py中创建视图

~~~python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, django. You're at the polls index.")  #最简单的视图，返回这一句话
~~~

##5、URL映射

前端访问这个视图是通过urls.py文件中进行配置

~~~python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),   #这个是应用内的路径映射
]
~~~

## 6、根目录URL配置指定创建的polls.urls模块

~~~python
from django.contrib import admin
from django.urls import include, path   
#指定polls.url需要在urlpatterns 列表里插入一个 include()
urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
~~~

函数 include() 允许引用其它 URLconfs。每当 Django 遇到 include()时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。 

- 启动项目查看结果

注意启动后访问的地址是：http://localhost:8000/polls/

![](E:\学习\django\第一篇\2.png)

## 7、path函数

函数 `path()`具有四个参数，两个必须参数：`route` 和 `view`，两个可选参数：`kwargs` 和 `name`。 

### `route`参数

route是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 `urlpatterns` 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。 

URLconf 在处理请求 `https://www.example.com/myapp/` 时，它会尝试匹配 `myapp/` 。处理请求 `https://www.example.com/myapp/?page=3` 时，也只会尝试匹配 `myapp/` 

### `view`参数

当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 `HttpRequest` 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入 

### `kwargs`参数

任意个关键字参数可以作为一个字典传递给目标视图函数 

### `name`参数

为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。 