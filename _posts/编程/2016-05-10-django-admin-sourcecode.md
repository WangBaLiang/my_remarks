---
layout: post
title: Django Admin源码阅读
categories: [编程]
tags: [Django]
disqus: y
---

##Django Admin源码阅读
###django.contrib.admin.options.py
ModelAdmin类是模型在Admin界面中的表现形式

		class ModelAdmin(BaseModelAdmin):
			...
			    def __init__(self, model, admin_site):
        			self.model = model
					# model例：<class 'django.contrib.auth.models.User'>
        			self.opts = model._meta
					# admin_site例：<django.contrib.admin.sites.AdminSite object at 0x2a5b150>
        			self.admin_site = admin_site 
        			super(ModelAdmin, self).__init__()

