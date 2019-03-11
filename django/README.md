### 一、Django简介

Django 是一个高级的 Python 网络框架，可以快速开发安全和可维护的网站。由经验丰富的开发者构建，Django负责处理网站开发中麻烦的部分，因此你可以专注于编写应用程序，而无需重新开发。

### 二、网站结构
#### 1、设计模型Model
Django通过对象关系映射器就能使用Python代码描述数据结构,models.py文件主要用于描述数据表，用python代码创建、检索、更新、删除数据库中的记录。
```python
# myproject/myapp/models.py
from Django.db import models
class book(models.Model):
    name = models.CharField(max_length=100)
    pub_dte = models.DateField()
    # 创建book模型，定义name、pub_date的属性
```
#### 2、设计视图Views
从web客户端接受HTTP请求并返回HTTP响应。在视图中引用被定义的模型，视图根据参数检索数据并呈现在加载的模板上。views.py文件包含了页面的业务逻辑。
```python
# myProject/myApp/views.py
from django.shortcuts import render
from .models import Person

def book_archive(request,year):
    book_list=Person.objects.filter(birth_year = year)
    context = {'year':year,'book_list':book_list}
    return render(request,'books/year_archive.html',context)
    # book_archive函数叫做视图，year_achive.html模板
```
#### 3、设计链接Urls
使用Urls需要建立URL和python回调函数的映射关系。urks.py指出什么样的URL调用什么视图。
```python
#myProject/myApp/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('books/<int:year>',views.year_archive),
]
# books/xxxx调用year_archive()函数，进入该链接会返回视图函数结果
```
#### 4、设计模板Templates
Django使用该强大的模板实现web页面,定义文件结构和布局的文本文件，对HTML模板添加任意类型的数据
```python
# /template/year_archive.html
{{% blocck title%}Books for {{ year }}{% bloch end%}
<h1>Artticles for {{years}}</h1>
{% for book in book_list %}
    <p>{{book.name}}</p>
    <p>Published {{ book.pub_date|date:"F j,Y"}}</p>
{% endfor %}
{% end block %}

# year_archive.html是模板，使用带逻辑声明的语言（如{% for book in book_list %}）将函数返回结果显示在网页上
```

* 这样的结构称为MVT模式