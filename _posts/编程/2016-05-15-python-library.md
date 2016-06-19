---
layout: post
title: Python库
categories: [编程]
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
            

###[functools](https://docs.python.org/2/library/functools.html)

###copy

        import copy
        a = [1, 2, 3, 4, ['a', 'b']]  #原始对象
        b = a  #赋值，传对象的引用
        c = copy.copy(a)  #对象拷贝，浅拷贝
        d = copy.deepcopy(a)  #对象拷贝，深拷贝
        a.append(5)  #修改对象a
        a[4].append('c')  #修改对象a中的['a', 'b']数组对象
        print 'a = ', a
        print 'b = ', b
        print 'c = ', c
        print 'd = ', d
        输出结果：
        a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
        b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
        c =  [1, 2, 3, 4, ['a', 'b', 'c']]
        d =  [1, 2, 3, 4, ['a', 'b']] 

1. 首先记住一点Python中一切皆对象，变量通过引用指向内存中的对象
2. 注意c的值，c[4]被改变了，因为a[4]指向内存中的另一个list对象，
3. copy后，c[4]也指向该list对象，a[4].append('c')其实是改变了该list对象
