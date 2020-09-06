---
title: Django2.2修改默认数据库为MySQL
tags:
- Python
- Django
- 配置
- MySQL
categories:
- 计算机
---



刚开始接触Django，发现其使用的数据库是SQLlite，但是我的电脑上已经有MySQL环境，于是打算将其换成MySQL环境，但是也因此踩了很多坑。
<!-- more -->
首先，需要在Django项目的settings.py文件里，修改DATABASE设置。

修改为：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite',  # 数据库名，先前创建的
        'USER': 'root',     # 用户名，可以自己创建用户
        'PASSWORD': '******',  # 密码
        'HOST': '127.0.0.1',  # mysql服务所在的主机ip
        'PORT': '3306',         # mysql服务端口
        'OPTIONS': {
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
        },
    }
}
```

具体的异常显示为：

```shell
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.
```

这里提到了一个包：mysqlclient，如果你的电脑环境中没有mysqlclient包，需要添加这个包：

如果电脑不是Windows环境，可以直接通过`pip install mysqlclient`添加这个包。

如果电脑是Windows环境，使用pip会出现错误，需要使用whl包安装。资源地址为

https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient 。在其中找到对应你的python版本和电脑字位的文件，下载。

下载完成后，进入此whl文件的文件夹，打开cmd（可以按住shift键，点击鼠标右键，在打开的菜单中打开cmd），使用`pip install [whl文件名+后缀]`的形式，安装mysqlclient包。

安装完成后，在Django项目的根目录下，使用命令`python manage.py migarate`命令。但是还是会有这个错误：

```shell
 File "D:\projectTools\anaconda\lib\site-packages\django\db\backends\mysql\base.py", line 36, in <module>
    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.
```

但是毫无疑问，我的mysqlclient包的版本为1.4.6，已经高于1.3.13版本，按理来说应该不会有这个报错。

先解决这个异常问题：打开产生错误的base.py文件，是有两行这样的代码：

```python
if version < (1, 3, 13):
    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
```

将这两行代码注释完成后，就不会产生版本错误的警告。（感觉是解决不了问题，就解决提出问题的人）

再次在django项目根目录运行`python manage.py migrate`命令，会产生另外一个错误：

```shell
 File "D:\projectTools\anaconda\lib\site-packages\django\db\backends\mysql\operations.py", line 146, in last_executed_query
    query = query.decode(errors='replace')
AttributeError: 'str' object has no attribute 'decode'
```

意思是query这个str变量没有decode这个方法，因此需要打开operations.py文件（它与上面打开的base.py在同一目录），在146行处，将`decode`改为`encode`，保存退出。

再次运行`python manage.py migrate`命令，发现命令终于成功运行：

```shell
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```

于是数据库就可以成功更改为MySQL。

以上是我参考网上其它开发者的解决办法，最后成功解决的。也有开发者提到，可能是因为我们使用的是pymysql包，它的版本是0.9.3，并且python解析时将其当作mysqlclient环境，直接读取其版本信息，才会导致一直报错，因此将版本检测的代码注释就不会有问题。