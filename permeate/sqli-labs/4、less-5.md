### less-5---双查询注入的原理

#### 一、原理和函数说明


1.1、原理：简单的一句话原理就是有研究人员发现，当在一个聚合函数，比如count函数后面如果使用分组语句就会把查询的一部分以错误的形式显示出来。

1.2、函数说明：
rand()是一个生成随机数的函数，他会返回0到1之间到一个值。
floor()是取整函数了，童鞋们应该都很熟悉了。
count()是一个聚合函数，用户返回符合条件的记录数量。

1.3、在数据库上实验一下，首先将刚才说明的三个函数实验一下
```
select count(*) from information_schema."tables";
```
![Alt text](./../../images/sql/less-5/count.png 'count')

```
select rand();
```
![Alt text](./../../images/sql/less-5/rand.png 'rand')

```
select floor(rand());

说明：floor的结果必然是0，因为rand()只会返回0-1之间的数，然后floor向下取整，结果必然是0
```
![Alt text](./../../images/sql/less-5/floorrand.png 'floorrand')

