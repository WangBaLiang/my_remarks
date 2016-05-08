---
layout: post
title: Django的问题记录
tags: [Django]
disqus: y
---

##单元测试的问题
###运行单元测试，报错：django.db.utils.IntegrityError: (1215, 'Cannot add foreign key constraint')
1. 数据库中查看,LATEST FOREIGN KEY ERROR相关信息可查看详情
	
		SHOW ENGINE INNODB STATUS;

2. 解决：把各个app下migrations的文件删除，重新makemigrations，运行单元测试即可
3. 待解决：为什么这样就行了，挖坑待填

###运行时指定settings.py
python manage.py test --setting=Gym.settings_test.py

###TransactionManagementError: An error occurred in the current transaction. You
cant execute queries until the end of the atomic block.
		
		try:
			user = User()
			....
			user.save()
		except IntegrityError:
			...
		# 如果上述代码抛出异常，the transaction was flagged as
		# broken，具体原理还没弄明白		

