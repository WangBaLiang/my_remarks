---
layout: post
title: 数据库性能优化之SQL语句
categories: [数据库]
tags: [数据库, 性能]
disqus: y
---

##操作符优化
1. IN 操作符
   * 先尝试转换成多个表连接，若转换不成功，执行in中的子查询，再执行外层查询；若转换成功，直接采用多表连接方式查询
   * 在业务密集的SQL当中尽量不采用IN操作符
   * _推荐_：用EXISTS 方案代替。
2. NOT IN操作符
   * 不能应用表的索引，不推荐使用
   * _推荐_：用NOT EXISTS 方案代替
3. IS NULL 或IS NOT NULL操作符
   * 索引不索引空值，不能用null做索引，任何包含null的列都不会被包含在索引
   * 任何在where子句中使用is null或is not null的语句优化器是不允许使用索引的。
   * _推荐_：
      * 用其它相同功能的操作运算代替，如：a is not null 改为 a>0 或a>’’等；          
      * 不允许字段为空，而用一个缺省值代替空值。
4. > 及 < 操作符（大于或小于操作符）
   * 执行A>2与A>=3，A>2会先找出为2的记录索引再进行比较，A>=3则直接找到=3的记录索引。
5. LIKE操作符
   * 通配符“*”在搜索词首出现，Oracle不会使用last_name的索引
   * LIKE ‘%5400%’ 这种查询不会引用索引，而是全表查询，而LIKE ‘X5400%’则会引用范围索引
6. UNION操作符

        select * from gc\_dfys 
        union 
        select * from ls\_jg_dfys

   * 这个SQL在运行时先取出两个表的结果，再用排序空间进行排序删除重复的记录，最后返回结果集，如果表数据量大的话可能会导致用磁盘进行排序。

        select * from gc_dfys 
        union all 
        select * from ls_jg_dfys

   * _推荐_：采用UNION ALL操作符替代UNION，因为UNION ALL操作只是简单的将两个结果合并后就返回。   

7. 联接列
   * 对于有联接的列，即使最后的联接值为一个静态值，优化器是不会使用索引的。我们一起来看一个例子，假定有一个职工表(employee)，对于一个职工的姓和名分成两列存放(FIRST\_NAME和LAST\_NAME)，现在要查询一个叫比尔.克林顿(Bill Cliton)的职工。  

        select * from employss where first\_name||''||last\_name ='Beill Cliton';

   * 使用上述SQL，系统优化器对基于last\_name创建的索引没有使用，_推荐使用下列语法_：

         where first\_name ='Beill' and last\_name ='Cliton';


8. Order by语句    
   * 在Order by语句的非索引项或者有计算表达式都将降低查询速度。
   * 重写order by语句以使用索引，也可以为所使用的列建立另外一个索引，同时应绝对避免在order by子句中使用表达式

9. NOT

         select * from employee where salary <> 3000;
         select * from employee where salary<3000 or salary>3000;
   * 第二种查询方案会比第一种查询方案更快些
   * 第二种查询允许Oracle对salary列使用索引，而第一种查询则不能使用索引

##SQL书写注意
1. 不同写法的SQL，如

        Select * from dlyx.zl_yhjbqk
        Select * from DLYX.ZLYHJBQK
        Select *  from DLYX.ZLYHJBQK
   * 从ORACLE共享内存SGA的原理，可以得出ORACLE对每个SQL 都会对其进行一次分析，并且占用共享内存，
   * 如果将SQL的字符串及格式写得完全相同，则ORACLE只会分析一次，共享内存也只会留下一次的分析结果，这不仅可以减少分析SQL的时间，而且可以减少共享内存重复的信息，ORACLE也可以准确统计SQL的执行频率。

2. WHERE后面的条件顺序
3. 查询表顺序的影响
   * 在FROM后面的表中的列表顺序会对SQL执行性能影响，在没有索引及ORACLE没有对表进行统计分析的情况下，ORACLE会按表出现的顺序进行链接，由此可见表的顺序不对时会产生十分耗服物器资源的数据交叉。（注：如果对表进行了统计分析，ORACLE会自动先进行小表的链接，再进行大表的链接）

##SQL语句索引的利用
1. 对条件字段的一些优化
    * 采用函数处理的字段不能利用索引

        substr(hbs\_bh,1,4)=’5400’，优化处理：hbs\_bh like ‘5400%’
        trunc(sk\_rq)=trunc(sysdate)， 优化处理：sk\_rq>=trunc(sysdate) and sk\_rq<trunc(sysdate+1)

    * 进行了显式或隐式的运算的字段不能进行索引

        ‘X’ || hbs\_bh>’X5400021452’，优化处理：hbs\_bh>’5400021542’
        sk\_rq+5=sysdate，优化处理：sk\_rq=sysdate-5

    * 条件内包括了多个本表的字段运算时不能进行索引

        ys\_df>cx\_df，无法进行优化 
        qc\_bh || kh\_bh=’5400250000’，优化处理：qc\_bh=’5400’ and kh_bh=’250000’

   * 选择最有效率的表名顺序(只在基于规则的优化器中有效)：
     * ORACLE 的解析器按照从右到左的顺序处理FROM子句中的表名，FROM子句中写在最后的表(基础表 driving table)将被最先处理，在FROM子句中包含多个表的情况下,你必须选择记录条数最少的表作为基础表。如果有3个以上的表连接查询, 那就需要选择交叉表(intersection table)作为基础表, 交叉表是指那个被其他表所引用的表.
   * WHERE子句中的连接顺序：
     * ORACLE采用自下而上的顺序解析WHERE子句,根据这个原理,表之间的连接必须写在其他WHERE条件之前, 那些可以过滤掉最大数量记录的条件必须写在WHERE子句的末尾.
   * SELECT子句中避免使用\* ：
     * ORACLE在解析的过程中, 会将’*’ 依次转换成所有的列名, 这个工作是通过查询数据字典完成的, 这意味着将耗费更多的时间。

##[参考](http://blog.jobbole.com/96996/)
