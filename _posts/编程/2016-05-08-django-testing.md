---
layout: post
title: Django单元测试
categories: [编程]
disqus: y
tags: [Django]
---

##[Django单元测试之testingtools](https://docs.djangoproject.com/en/1.9/topics/testing/tools/)
###测试客户端

####作用
1. 伪造GET和POST请求解析response
2. 查看重定向的顺序，每一步的状态码
3. 测试带已渲染模板的response(包括模板和一些值)

####目的
1. 测试模板的渲染是否正常，包括是不是想要的模板、传递的值是否正确
2. 支持一些其他的框架,如in-browser frameworks like[Selenium](http://docs.seleniumhq.org/),挖坑待填


###伪造请求
####class Client(enforce_csrf_checks=False, **defaults)
* 不需要任何参数，但是可以自行构造请求头，之后该请求头会传递到get和post方法

		c = Client(HTTP_USER_AGENT='Mozilla/5.0')

* enforce_csrf_checks用于测试CSRF保护
* 该类的实例方法
	* get(path, data=None, follow=False, secure=False, **extra)

			# /customers/details/?name=fred&age=7
			c = Client()
			c.get('/customers/details/', {'name': 'fred', 'age': 7})
            # 伪造Ajax请求
			c.get('/customers/details/', {'name': 'fred', 'age': 7},
					HTTP_X_REQUESTED_WITH='XMLHttpRequest')
			# 跟踪重定向
			response = c.get('redirect_me', follow=True)
			print response.redirect_chain
			# [('http://testserver/next/', 302),('http://testserver/final/', 302)]
			# 伪造HTTPS请求
			c.get('/customers/details/', secure=True)
	* post(path, data=None, content_type=MULTIPART_CONTENT, follow=False, secure=False, **extra)
			
			# /login/ with post data: name=fred&passwd=secret
			c = Client()
			c.post('login', {'name':'fred', 'passwd': 'secret'})
			# content_type，默认multipart/form-data
			# post文件
			with open('wishlist.doc') as fp:
				c.post('/customers/wishes/', {'name': 'fred', 'attachment':fp})
			# 可以在post方法中的url中传递其他的参数；request.GET中有visitor
			c.post('/login/?visitor=true', {'name': 'fred', 'passwd':'secret'})

	* head(path, data=None, follow=False, secure=False, **extra)
	* options(path, data='', content_type='application/octet-stream',follow=False, secure=False, **extra)
	* put(path, data='', content_type='application/octet-stream', follow=False, secure=False, **extra)
	* patch(path, data='', content_type='application/octet-stream', follow=False, secure=False, **extra)
	* delete(path, data='', content_type='application/octet-stream', follow=False, secure=False, **extra)
	* trace(path, follow=False, secure=False, **extra)
	* login(\**credentials)
		* Inactive users (is_active=False)无法登陆

				# 默认认证后台					
				c = Client()
				c.login(username='fred', password='secret')
				# 其他的认证后台
				user = authenticate(username=name, password=password)
				c.login(request, user)

	* force_login(user, backend=None)
		* 伪造登陆，没有认证环节，Inactive users也能登陆
		* 优点是比login快，因为没有使用密码哈希算法，当然，也能通过使用 a weaker hasher while testing来加速login方法
	* logout()
		
###Testing response
####get和post方法返回一个Reponse，不同于view返回的HttpResponse，有以下属性
* client
* content，相应的主体
* context，使用DjangoTemplates的template Context instance
* json(\**kwargs)，可以传递额外的参数给json.loads()，如果Content-Type不是"applicatino/json"，会抛出ValueError

		response = client.get('/foo/')
		response.json()['name']

		
* request, The request data that stimulated the response.
* wsgi_request
* status_code,The HTTP status of the response, as an integer.
* templates
* resolver_match

###Exceptions

###Persistent state
The test client is stateful. If a response returns a cookie, then that cookie will be stored in the test client and sent with all subsequent get() and post() requests.

###可用的test case classes
Django扩展了Python的unit test
![Django test](django_unittest_classes_hierarchy.svg)
	* SimpleTestCase
	* TransactionTestCase
	* TestCase
	* LiveServerTestCase

到这儿基本就能写出Django测试，挖坑，待填

