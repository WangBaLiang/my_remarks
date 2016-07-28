---
layout: post
title: Django的问题记录
categories: [编程]
tags: [Django]
disqus: y
---
##基本语法
###static文件加载
使用static标签来加载js文件，直接使用文件路径会导致请求js文件的路径错误。


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

###在编写admin的测试用例的时候，在save_model方法中一直看不到request.user
1. 测试用例继承自django.test.TestCase，定义了setUp方法：


		def setUp(self):
        user = User.objects.create_user("wuruimiao",
                                        "11111111@qq.com",
                                        "********",
                                        is_superuser=True,
                                        is_staff=True)
        self.client = ClientTest()
        self.client.login(username="wuruimiao", password="19941103champion")

2. 需要注意的是指定is_staff为True，否则是无法使用admin后台的

###在为admin后台添加url时，一直找不到指定的url
1. 开始是这样写的

		def get_urls(self):
			from django.conf.urls import url
			urls = super(OrderManage, self).get_urls()
			urls += [url(r'^info/$', self.info, name="Appointment_manage_order_info")]
			return urls

2. 后来换成这样，就可以了

		...
		myurl = [url(r'^info/$', self.info, name="Appointment_manage_order_info")]
		return myurl+urls

3. urls.append(...)这种写法也是不行的
4. return urls+myurl 不行
5. 可见return的url顺序有要求，具体原理参见django url

###使用Thrift的时候，如果从服务取不到数据，检查下thrift定义文件
