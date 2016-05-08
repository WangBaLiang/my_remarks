---
layout: post
title: Django认证源码
tags: [Django, 源码]
disqus:y
---

##Django auth阅读
###django.contrib.auth.__init__.py

		def authenticate(**credentials):
			"""
			If the given credentials are valid, return a User object.
			"""
			for backend, backend_path in _get_backends(return_tuples=True):
				try:
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
					credentials=_clean_credentials(credentials)
		)"

