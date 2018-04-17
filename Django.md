## 第一节：请求与响应

### 一、创建项目
``` bash
django-admin startproject < project name >
```

#### 项目结构
``` bash
.
├── manage.py # Django 命令行管理工具
└── mysite    # Python 包模块目录  
    ├── __init__.py # Python 包初始化文件
    ├── settings.py # 设置及配置文件
    ├── urls.py     # Django项目的URL声明
    └── wsgi.py     # web服务网关接口
```


### 二、运行服务(支持自动重载)
``` bash
python manage.py runserver 
```

### 三、在Django项目中创建应用
```bash
python manage.py startapp < app name >
```

#### 在项目中应用的基本目录结构
```
    polls   # 应用目录，应用名称polls
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── __init__.py
    ├── models.py
    ├── tests.py
    ├── urls.py  # url与视图函数映射，需要单独创建该文件
    └── views.py # 视图函数所在文件
```

## 第二节：模型与管理站点

### 一、数据库设置

进入项目`settings.py`文件

1. 配置mysql数据库参数
``` py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',
        'USER': 'test',
        'PASSWORD': 'jiangink',
        'HOST': '127.0.0.1'
    }
}
```
2. 创建对应的数据库

``` sql
create database mysite;
``` 
3. 迁移构建数据库表

`migrate`根据`settings.py`中的`DATABASES`中的数据库名及`INSTALLED_APP`中的应用表结构创建数据库及相应的表；
``` bash
python manage.py migrate
```

### 二、创建数据模型

1. 在创建的应用目录下的`models.py`中定义数据模型

2. 在项目中`settings.py`的`INSTALLED_APPS`中，添加应用的相关配置

3. 激活并创建所在应用中数据模型的迁移文件（仅仅生成数据模型迁移文件）
``` bash
python manage.py makemigrations polls
```
4. 查看迁移文件所对应的SQL语句（相当于将迁移文件转译为SQL语句,便于SQL执行语句的检查）
``` bash
python manage.py sqlmigrate polls 0001
```
5. 执行数据模型的迁移
``` bash
python manage.py migrate
```

6. 数据模型的修改变更三大步骤：
    * 修改你的模型文件models.py
    * 运行`python manage.py makemigrations`, 为变更创建迁移文件
    * 运行`python manage.py migrate`, 将变更更新到数据库中

### 三、Django交互式shell使用

#### 进入django manage shell环境
``` bash
python manage.py shell
```
...

### 四、管理后台

#### 创建管理员用户
``` bash
python manage.py createsuperuser

# user: test
# pass: qwer1234
```

#### 将应用中的数据模型注册到管理后台中

如下是将Question模型注册到后台管理中：
``` py
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

## 第三节：视图与模板

### 视图函数

* 所在应用中的`views.py`中
* 视图函数主要的返回: 返回的是一个 HttpResponse ，或者抛出一个异常。
```
# 通常的模板渲染方式
from django.template import loader
def index(request):
    template = loader.get_template('polls/index.html') # 1. 加载模板
    context = { 'test_data': [1,2,3,4] }    # 2. 设置上下文数据
    return HttpResponse(template.render(context, request)) # 3. 渲染返回

# 快捷的模板渲染方式 render()
from django.shortcuts import render
def index(request):
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context) # 模板渲染快捷方式

# 通常的抛出异常方式
from django.http import Http404
from django.shortcuts import render
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})

# 快捷的抛出异常方式 get_object_or_404()
from django.shortcuts import get_object_or_404, render
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})

```
* 采用`urls_name`替换模板中硬编码url
```
# 将原硬编码url
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
# 替换为url name
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>

```
* 为应用url添加命名空间
```
# 设置应用urls.py文件
app_name = 'app name'
```

## 第四节：表单与通用视图

### 


