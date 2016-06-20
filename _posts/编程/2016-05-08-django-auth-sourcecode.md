---
layout: post
title: Django auth阅读
categories: [编程]
tags: [Django, 源码]
disqus: y
---

##Django auth阅读
###django.contrib.auth.models.py
定义了认证用到的User，User继承自AbstractUser，AbstractUser基类继承自AbstractBaseUser和PermissionsMixin)    

###django.contrib.auth.backends.py
定义了两个认证后台类，ModelBackend和RemoteUserBackend
####ModelBackend

		def authenticate(self, username=None, password=None, **kwargs):
				# return django_apps.get_model(settings.AUTH_USER_MODEL)
				# UserModel：django.contrib.auth.models.User
		        UserModel=get_user_model()
		        if username is None:
		            username=kwargs.get(UserModel.USERNAME_FIELD)
		        try:
		            user=UserModel._default_manager.get_by_natural_key(username)
		            if user.check_password(password):
		                return user
		        except UserModel.DoesNotExist:
		            # Run the default password hasher once to reduce the timing
		            # difference between an existing and a non-existing user (#20760).
		            UserModel().set_password(password)

###django.contrib.auth.__init__.py
1. authenticate

		def _get_backends(return_tuples=False):                                          
    		backends = []                                                                
    		for backend_path in settings.AUTHENTICATION_BACKENDS:
    			# settings.AUTHENTICATION_BACKENDS默认是['django.contrib.auth.backends.ModelBackend']
    			# load_backend调用django.utils.module_loading的import_string(path)方法，该方法返回ModelBackend类，然后创建一个实例backend
        		backend = load_backend(backend_path)                                     
        		backends.append((backend, backend_path) if return_tuples else backend)   
    		if not backends:                                                             
        		raise ImproperlyConfigured(                                              
            		'No authentication backends have been defined. Does '                
            		'AUTHENTICATION_BACKENDS contain anything?'                          
        	)              
        	# backends = [(ModelBackend类实例, 'django.contrib.auth.backends.ModelBackend')]
    		return backends
							
		def authenticate(**credentials):
			"""
			If the given credentials are valid, return a User object.
			"""
			for backend, backend_path in _get_backends(return_tuples=True):
			# ModelBackend类实例, 'django.contrib.auth.backends.ModelBackend'
				try:
					# 相当于把**credentials传递给ModelBackend类实例的authenticate调用
					inspect.getcallargs(backend.authenticate, **credentials)
				except TypeError:
					# This backend doesn't accept these credentials as arguments. Try
					# the next one.
					continue
				try:
					user = backend.authenticate(**credentials)
				except PermissionDenied:
					# This backend says to stop in our tracks - this user should not be
					# allowed in at all.
					return None
				if user is None:
					continue
				# Annotate the user object with the path of the backend.
				user.backend = backend_path
				return user

			# The credentials supplied are invalid to all backends, fire signal
			user_login_failed.send(sender=__name__,
					credentials=_clean_credentials(credentials))


		def load_backend(path):                                                          
    		return import_string(path)()
    		
    	def import_string(dotted_path):
    		"""
			Import a dotted module path and return the attribute/class designated by the
    		last name in the path. Raise ImportError if the import failed.
    		"""
    		try:
        		module_path, class_name=dotted_path.rsplit('.', 1)
    		except ValueError:
        		msg="%s doesn't look like a module path" % dotted_path
        		six.reraise(ImportError, ImportError(msg), sys.exc_info()[2])
    		module=import_module(module_path)
    		try:
        		return getattr(module, class_name)
    		except AttributeError:
        		msg='Module "%s" does not define a "%s" attribute/class' % (module_path, class_name)
        		six.reraise(ImportError, ImportError(msg), sys.exc_info()[2]) 
        		#sys.exc_info：return thread-safe information about the current exception
