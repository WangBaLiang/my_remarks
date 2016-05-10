---
layout: post
title: Python库
disqus: y
---

##Python库
###collections
1. class collections.defaultdict([default_factory[, ...]])
内置dict的子类
    
        >>> s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
        >>> d = defaultdict(list)
        >>> for k, v in s:
        ...     d[k].append(v)
        ...
        >>> d.items()
        [('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
        # 循环遍历第一次找到'yellow'时，d使用default_factory——list创建一个空列表，然后调用list.append()
        # 再次找到'yellow'时，找到'yellow'对应的列表，调用list.append()
        # 这比下面的方法快
        d = {}
        for k, v in s:
            d.setdefault(k, []).append(v)
        # D.get(k[,d]) -> D[k] if k in D, else d.  d defaults to None.
        # D.setdefault(k[,d]) -> D.get(k,d), also set D[k]=d if k not in D
        # d.setdefault(k, []).append(v)即先在d中查找是否有k这个key，没有，创建一个空list，把v append进去
            

        
