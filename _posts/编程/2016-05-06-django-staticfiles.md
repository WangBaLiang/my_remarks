---
layout: post
title: Django静态文件部署
categories: [编程]
tags: [Django]
disqus: y
---

##官方文档1.9
###[如何部署静态文件](https://docs.djangoproject.com/en/1.9/howto/static-files/)
1. settings.py文件，INSTALLED_APPS配置项有django.contrib.staticfiles；
2. settings.py文件，STATIC_URL = '/static/'；
3. 在模板中使用load staticfiles
4. 静态文件位于app下的static目录，也可配置STATICFILES_DIRS来指定其他位置（推荐配置一个专门 目录来存放，Django默认使用第一个找到的文件，即使还存在其他同名文件）

###[The staticfiles app](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles)
* Django使用django.contrib.staticfiles管理静态文件，即前一部分中INSTALLED_APPS的要求配置项
* [STATICFILES\_FINDERS](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATICFILES_FINDERS)，默认配置1和2，没有3
    1. 'django.contrib.staticfiles.finders.FileSystemFinder'搜索STATICFILES_DIRS位置的静态文件
    2. 'django.contrib.staticfiles.finders.AppDirectoriesFinder'搜索app下static目录的静态文件
    3. ’django.contrib.staticfiles.finders.DefaultStorageFinder‘搜索DEFAULT\_FILE\_STORAGE位置的文件
* django.contrib.staticfiles提供了三个命令
    1. collectstatic 通过配置的FINDERS搜索文件，然后将静态文件放到STATIC\_ROOT位置下
    2. findstatic 搜索静态文件，有--first， --verbosity选项
    3. runserver 当INSTALLED_APPS配置项有django.contrib.staticfiles时，覆盖默认的runserver。配置项有--nostatic，--insecure（不推荐使用）

###[生产环境静态文件部署](https://docs.djangoproject.com/en/1.9/howto/static-files/deployment/)
