---
title: Django模型设置主键自增
tags:
- Python
- Django
- MySQL
categories:
- 计算机
---

在学习Django的模型过程中，设置主键自增的问题一直困扰我很久，但是现在还没有完全解决。

主要的问题出在使用IntegerField或者AutoField上。
<!-- more -->
一般来讲，是在模型文件中使用`id = IntegerField(primerykey=True)`即可设置为自增主键。这里我的类定义的很简单：

```python
class nb(models.Model):
    i = models.IntegerField(primary_key=True)
    title_name = models.CharField(max_length=50)
    image_alt = models.CharField(max_length=50)
```

然后通过python manage.py makemigrations生成迁移文件以及python manage.py migrate执行迁移后，我在MySQL中查看了一下我的表信息，然而却是这样：

```mysql
+------------+-------------+------+-----+---------+-------+
| Field      | Type        | Null | Key | Default | Extra |
+------------+-------------+------+-----+---------+-------+
| i          | int(11)     | NO   | PRI | NULL    |       |
| title_name | varchar(50) | NO   |     | NULL    |       |
| image_alt  | varchar(50) | NO   |     | NULL    |       |
+------------+-------------+------+-----+---------+-------+
```

可以看到，在主键i的信息描述中，只有标识其为主键，但是并没有标识其为自增。事实上，此时如果在python shell中添加实例时，且不指定i的值，在对实例进行save()时，就会报错，不能成功save。

然而，如果我使用的是AutoField，即：

```python
class nb(models.Model):
    i = models.AutoField(primary_key=True)
    title_name = models.CharField(max_length=50)
    image_alt = models.CharField(max_length=50)
```

迁移之后，再次查看我的表信息，结果如下：

```mysql
+------------+-------------+------+-----+---------+----------------+
| Field      | Type        | Null | Key | Default | Extra          |
+------------+-------------+------+-----+---------+----------------+
| i          | int(11)     | NO   | PRI | NULL    | auto_increment |
| title_name | varchar(50) | NO   |     | NULL    |                |
| image_alt  | varchar(50) | NO   |     | NULL    |                |
+------------+-------------+------+-----+---------+----------------+
```

这一次主键的信息中有标识其为自增。在添加实例时，就可以不显式添加主键的值，它自己会自增。不过第一次添加时需要指定主键的值，以作为自增的起始值。当然也可以不指定，它自己会默认为1，但是会跳出一个warnning。

虽然看起来到这里好像就结束了，但是我在查阅互联网上其它人对于设置主键自增的描述，都是说只需要使用IntegerField就可以。e.g. https://blog.csdn.net/mapoor/article/details/8609660

然而我试过了许多次，但是似乎都无效。。。

因此我现在只好就使用AutoField。现在我认为可能是django版本的问题，也可能是我使用的mysql包是pymysql，而他们使用的是mysqlclient。但是现在这些都只是猜测，我还没有去证实。主要是现在人在家里躺废了，干什么事情都没有动力，甚至我连官方文档也没有看，因为觉得看英文太麻烦了。。。以后什么时候有精神了再看看是什么原因吧。

希望这一次的疫情能尽快控制下来。