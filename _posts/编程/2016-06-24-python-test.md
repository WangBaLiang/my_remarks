---
layout: post
title: Python测试
categories: [编程]
tags: [python 测试]
disqus: y
---
##性能测试
###使用time测量时间
timer.py

        import time
        class Timer(object):
            def __init__(self, verbose=False):
                self.verbose = verbose

            def __enter__(self):
                self.start = time.time()
                return self

            def __exit__(self, *args):
                self.end = time.time()
                self.secs = self.end - self.start
                self.msecs = self.secs * 1000  # millisecs
                if self.verbose:
                    print 'elapsed time: %f ms' % self.msecs

test.py

        from timer import Timer
        with Timer() as t:
            ......
        print t.secs
        print t.msecs

###使用line_profiler分析执行频率
pip install line_profiler     
为函数添加@profile装饰器，不用导入其他东西      
kernprof.py -l -v test.py      


###使用memory_profiler分析内存占用
pip install -U memory\_profiler     
pip install psutil    
为函数添加@profile装饰器，不用导入其他东西      
python -m memory\_profiler test.py     


