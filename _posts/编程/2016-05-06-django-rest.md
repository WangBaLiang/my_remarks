---
layout: post
title: Django REST框架
categories: [编程]
tags: [Django REST]
disqus: y
---

##[Django REST框架](http://www.django-rest-framework.org/)
##安装
1. pip install djangorestframework
2. pip install markdown  # Markdown support for the browsable API.
3. pip install django-filter  # Filtering support

##使用
1. Add 'rest\_framework' to your INSTALLED\_APPS setting.
            
        urlpatterns = [
            url(r'^api-auth/', include('rest\_framework.urls',  namespace='rest_framework'))
        ]
       

2. If you're intending to use the browsable API you'll probably also want to add REST framework's login and logout views. Add the following to your root urls.py file.
