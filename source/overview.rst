.. _overview:

==================================================
Django at a glance 初识Django
==================================================

Because Django was developed in a fast-paced newsroom environment, it was designed to make common Web-development tasks fast and easy. Here’s an informal overview of how to write a database-driven Web app with Django.

因为Django是在一个快节奏的新闻编辑环境下开发完成的，它的设计让通用Web开发任务变得快速而且高效。这里我们可以学习一下如何用Django写一个数据库驱动的Web应用。

The goal of this document is to give you enough technical specifics to understand how Django works, but this isn’t intended to be a tutorial or reference – but we’ve got both! When you’re ready to start a project, you can start with the tutorial or dive right into more detailed documentation.

本文档的目标是提供足够多的技术细节让您能够理解Django是如何工作的, 但这不是要做教程或者参考资料 - 但实际上两者的作用都有! 当你准备好开始一个项目的时候, 你可以跟着教程开始学习或者深入更详细文档。

Design your model 设计模型
==========================

Although you can use Django without a database, it comes with an object-relational mapper in which you describe your database layout in Python code.

尽管您不用数据库就能使用Django, 它还是提供了一个对象-关系映射器, 让您可以使用Python代码描述您的数据库结构。

The data-model syntax offers many rich ways of representing your models – so far, it’s been solving many years’ worth of database-schema problems. Here’s a quick example:

数据-模型语法提供了丰富的方式来表述您的模型 - 目前, 它已经解决了多年来的数据库-模式问题。我们来看看例子: ::

    mysite/news/models.py

    from django.db import models
    
    class Reporter(models.Model):
        full_name = models.CharField(max_length=70)
    
        def __str__(self):              # __unicode__ on Python 2
            return self.full_name
    
    class Article(models.Model):
        pub_date = models.DateField()
        headline = models.CharField(max_length=200)
        content = models.TextField()
        reporter = models.ForeignKey(Reporter)
    
        def __str__(self):              # __unicode__ on Python 2
            return self.headline

Install it 安装
===============

Next, run the Django command-line utility to create the database tables automatically: ::

下一步, 运行Django命令行工具来自动创建数据库表:

$ python manage.py migrate

The migrate command looks at all your available models and creates tables in your database for whichever tables don’t already exist, as well as optionally providing much richer schema control.

migrate命令查询所有可用数据模型并在数据库中创建表,只要表格不是数据库中存在的, 以及有选择的提供更丰富的模式控制。

Enjoy the free API 享用免费的API 
================================

With that, you’ve got a free, and rich, Python API to access your data. The API is created on the fly, no code generation necessary:

这样，您就获得了一个免费、丰富的Python应用编程接口访问您的数据。API是在动态过程中创建的, 因此没有代码生成的必要: ::

    # Import the models we created from our "news" app
    >>> from news.models import Reporter, Article
    
    # No reporters are in the system yet.
    >>> Reporter.objects.all()
    []
    
    # Create a new Reporter.
    >>> r = Reporter(full_name='John Smith')
    
    # Save the object into the database. You have to call save() explicitly.
    >>> r.save()
    
    # Now it has an ID.
    >>> r.id
    1
    
    # Now the new reporter is in the database.
    >>> Reporter.objects.all()
    [<Reporter: John Smith>]
    
    # Fields are represented as attributes on the Python object.
    >>> r.full_name
    'John Smith'
    
    # Django provides a rich database lookup API.
    >>> Reporter.objects.get(id=1)
    <Reporter: John Smith>
    >>> Reporter.objects.get(full_name__startswith='John')
    <Reporter: John Smith>
    >>> Reporter.objects.get(full_name__contains='mith')
    <Reporter: John Smith>
    >>> Reporter.objects.get(id=2)
    Traceback (most recent call last):
        ...
    DoesNotExist: Reporter matching query does not exist.
    
    # Create an article.
    >>> from datetime import date
    >>> a = Article(pub_date=date.today(), headline='Django is cool',
    ...     content='Yeah.', reporter=r)
    >>> a.save()
    
    # Now the article is in the database.
    >>> Article.objects.all()
    [<Article: Django is cool>]
    
    # Article objects get API access to related Reporter objects.
    >>> r = a.reporter
    >>> r.full_name
    'John Smith'
    
    # And vice versa: Reporter objects get API access to Article objects.
    >>> r.article_set.all()
    [<Article: Django is cool>]
  
    # The API follows relationships as far as you need, performing efficient
    # JOINs for you behind the scenes.
    # This finds all articles by a reporter whose name starts with "John".
    >>> Article.objects.filter(reporter__full_name__startswith='John')
    [<Article: Django is cool>]
    
    # Change an object by altering its attributes and calling save().
    >>> r.full_name = 'Billy Goat'
    >>> r.save()
    
    # Delete an object with delete().
    >>> r.delete()

.. toctree::
   :maxdepth: 2
