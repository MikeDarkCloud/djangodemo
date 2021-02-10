# Django学习笔记（三）-管理页面

##1、创建管理员账号

~~~python
#创建管理员账号命令
python manage.py createsuperuser
~~~

创建管理员用户之后启动项目访问<http://127.0.0.1:8000/admin/> ，使用创建好的管理员账户密码登录

![](E:\学习\django\第三篇\1.png)

![](E:\学习\django\第三篇\2.png)

##2、将投票应用添加到管理页面中

在polls/admin.py文件，添加代码如下：

~~~python
from django.contrib import admin

from .models import Question

admin.site.register(Question)
~~~

添加后重新启动项目就可以在管理页面看到polls应用.

![](E:\学习\django\第三篇\3.png)

django会根据`Question` 模型中自动生成的，可以直接编辑Question对象。

![](E:\学习\django\第三篇\4.png)

## 3、编写视图

Django 中的视图的概念是一类具有相同功能和模板的网页的集合。

在 Django 中，网页和其他内容都是从视图派生而来。每一个视图表现为一个简单的 Python 函数（或者说方法，如果是在基于类的视图里的话）。Django 将会根据用户请求的 URL 来选择使用哪个视图（更准确的说，是根据 URL 中域名之后的部分）。 

在polls/viws.py中编写视图

~~~python
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
~~~

将编写好的视图添加到polls.urls模块中。

~~~python
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
~~~

访问网页的处理过程如当访问 "/polls/34/" ，Django 将会载入 `mysite.urls` 模块，因为这在配置项 [`ROOT_URLCONF`](https://docs.djangoproject.com/zh-hans/2.2/ref/settings/#std:setting-ROOT_URLCONF) 中设置了。然后 Django 寻找名为 `urlpatterns` 变量并且按序匹配正则表达式。在找到匹配项 `'polls/'`，它切掉了匹配的文本（`"polls/"`），将剩余文本——`"34/"`，发送至 'polls.urls' URLconf 做进一步处理。 

- 视图

每个视图必须要做的只有两件事：返回一个包含被请求页面内容的 `HttpResponse`对象，或者抛出一个异常，比如 `Http404`。 

一个视图例子：

实现访问index页面返回发布日期排序最近5个投票问题

~~~python
#polls/index
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
~~~

重启项目后访问http://localhost:8000/polls/可看到如下页面

![](E:\学习\django\第三篇\5.png)

我们得到是一个固定的页面，如果需要修改需要直接修改代码，显然这样是不方便的，这就需要创建一个视图，将页面的设计从代码中分离出来。

1、首先需要 `polls` 目录里创建一个 `templates` 目录。Django 将会在这个目录里查找模板文件。 

2、在创建的 `templates` 目录里，再创建一个目录 `polls`，然后在其中新建一个文件 `index.html` 。 

备注：为什么要这样创建？因为项目的 `TEMPLATES`配置项描述了 Django 如何载入和渲染模板。默认的设置文件设置了 `DjangoTemplates` 后端，并将 `APP_DIRS`设置成了 True。这一选项将会让 `DjangoTemplates` 在每个 `INSTALLED_APPS`文件夹中寻找 "templates" 子目录。这就是为什么尽管我们没有像在第二部分中那样修改 DIRS 设置，Django 也能正确找到 polls 的模板位置的原因 

index.html文件内容如下：

~~~python
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
~~~

使用index.html文件需要修改`polls/views.py`  文件

~~~python
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))

#上述代码的作用是，载入 polls/index.html 模板文件，并且向它传递一个上下文(context)。这个上下文是一个字典，它将模板内的变量映射为 Python 对象。
~~~

修改后重启项目访问http://localhost:8000/polls/可看到如下页面

![](E:\学习\django\第三篇\6.png)

## 4、render函数

render函数作用是载入模板，填充上下文，再返回由它生成的 HttpResponse对象

` polls/views.py`文件中的index函数可以改写成如下：

~~~python
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
~~~

## 5、get_object_or_404函数

get_object_or_404函数，尝试用 get() 函数获取一个对象，如果不存在就抛出 Http404错误也是一个普遍的流程 

detail函数可以写成如下

~~~python
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
~~~

也有 get_list_or_404() 函数，工作原理和 get_object_or_404() 一样，除了 get() 函数被换成了 filter() 函数。如果列表为空的话会抛出 Http404异常。 



