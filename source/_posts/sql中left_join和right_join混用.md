title: 'sql中left_join和right_join混用'
date: 2014-08-08 10:02
tags:
- sql
- select
categories:
- 数据库
---
left join(左联接) 返回包括左表中的所有记录和右表中联结字段相等的记录 

right join(右联接) 返回包括右表中的所有记录和左表中联结字段相等的记录

inner join(等值连接) 只返回两个表中联结字段相等的行



如上所言,我们只做一下left join的实验,right join和left join 类似

测试一共三张表,a  b   c
![]()

表结构和数据分别如下

a表                                                                  b表                                                              c表
![]()         ![]()          ![]()

然后我们来试一下左连接

1,  我们来查询a b两张表,连接他们aid=bid的选项
sql>>SELECT \*  FROM a   LEFT JOIN b on aid=bid


![]()



看图很好理解,因为是a左连接b,所以b中为空的元素 ,在查询结果中以NULL的形式补全

同理可得

sql>>SELECT \* FROM a LEFT JOIN c on aid=cid
![]()


sql>>SELECT \* FROM c LEFT JOIN b on bid=cid
sql>>SELECT \* FROM c LEFT JOIN b on cid=bid


![]()



也就是on后面的顺序无所谓,无论是bid=cid还是cid=bid,重点是前面的left join还是right join







2,我们来试一下如果三张表左连接呢,会有什么情况?

sql>>SELECT \* FROM a

           LEFT JOIN b on bid=aid 

           LEFT JOIN c on aid=cid
![]()



sql>>SELECT \* FROM a LEFT JOIN b on bid=aid LEFT JOIN c on bid=cid
![]()



3,如果我用了一个right join呢?

sql>>SELECT \* FROM a LEFT JOIN b on bid=aid RIGHT JOIN c on bid=cid
![]()



这里该怎么理解呢?
感谢[http://blog.csdn.net/sqlserverdiscovery/article/details/6893288](http://blog.csdn.net/sqlserverdiscovery/article/details/6893288)

我们理解的时候可以把它拆成两部分去理解

比如第一部分
sql>>SELECT \* FROM a LEFT JOIN b on bid=aid


![]()



然后再思考第二部分,假如上图是一张表  我们叫它a_b

你可以新建一个视图,就可以真成为一张虚表了

sql>>create  VIEW a_b  as (SELECT \* FROM a LEFT JOIN b on bid=aid)
![]()



然后我们继续做下面一部分

sql>>select \* from a_b right join c on bid=cid
![]()



可见,与上面的结果是一样的,当然这是我们理解的过程,实际数据库服务器执行的时候可能不是这样的,它会做一些优化,提高效率















