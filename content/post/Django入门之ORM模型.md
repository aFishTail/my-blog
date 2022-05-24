---
title: "Django入门之ORM模型"
date: 2021-11-24T16:47:50+08:00
draft: true
---

> ORM的好处

- 面向对象的编程思想，方便扩充
- 少写（几乎不写）SQL，提升开发效率
- 支持多种类型的数据库，方便切换
- ORM技术成熟，能解决绝大部分问题

> 章节概要

- 模型的创建

- 模型介绍及配置

- 模型同步migrate等

- 常见的ORM字段类型

- 模型的元数据

- 外键关联类型

- 复合类型

> 学习目标

- 掌握Django中ORM的配置
- 理解数据库设计中的外键关联
- 掌握模型的设计和代码实现
- 掌握模型同步到数据库表的使用
- 掌握ORM中的关联关系（一对一、一对多、多对多、复合关联）

## 课程目标

## 模型介绍及配置


## 本节内容

 了解 Django中ORM以及它所支持的数据库

 了解 Django中ORM与Flask中ORM的区别

 掌握 Django中ORM的配置

## Django ORM 模型

#### 张三

```
姓名
性别
```
```
年龄
用户名
```
```
密码
```
```
字段 类型 长度
name varchar 64
sex char 1
age int
username varchar 64
password varchar 256
```

# Django ORM 支持的数据库

```
Django
ORM
```
## 与Flask的区别

 Django自带ORM

 Flask需要使用扩展（ SQLAlchemy只是其中一个）


## Django ORM 配置

 项目配置（settings.py）

```
 安装依赖
```
## Django ORM 配置

```
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.mysql',
'NAME': 'mydatabase',
'USER': 'mydatabase_user',
'PASSWORD': 'mypassword',
'HOST': '127.0.0.1',
'PORT': '3306',
}
}
```
- 配置示例


## 配置选项

```
 default——默认的数据库，可配置多个数据，使用名称来区分
```
```
'django.db.backends.postgresql'
'django.db.backends.mysql'
'django.db.backends.sqlite3'
'django.db.backends.oracle'
```
- ENGINE——数据库引擎

## 配置选项

- NAME——数据库名称

- USER——数据库登录用户名

- PASSWORD——数据库登录密码

- HOST——数据库访问地址

- PORT——数据库访问端口


## 配置选项

 sqlite3的配置选项

#### DATABASES = {

```
'default': {
'ENGINE': 'django.db.backends.sqlite3',
'NAME': 'mydatabase.db'
}
}
```
#### 只需要指定数据库引擎和数据库文件名称即可

## Django ORM 配置

 项目配置（settings.py）

```
 安装依赖
pip install mysqlclient
```

#### 要想使用ORM，就得先配置好

## 小结

## 常见的ORM字段类型


## 本节内容

- 了解 ORM中常见的字段类型

- 掌握 ORM中字段类型的配置选项（可选参数）

```
 了解 CharField、 DateTimeField的参数传递
```
## Django ORM 类型映射

**字段 类型 长度**
name varchar 64
sex char 1
age int
username varchar 64
password varchar 256

- 回顾：数据库中的字段类型

```
字段 描述
char 固定长度字符
varchar 可变长度字符串
int 整型
datetime 时间
float/decimal 浮点数(小数）
text 长文本
```

## 数据库常见类型

```
字段 描述
char 固定长度字符
varchar 可变长度字符串
text 长文本
float/decimal 浮点数(小数）
int/tinyint 整型
date/time/datetime 日期/时间
```
## 模型代码

- 示例

```
class User(models.Model):
""" 用户模型 """
name = models.CharField('姓名', max_length=64)
age = models.PositiveIntegerField('年龄', default=0)
```

## 文本

 CharField、TextField——字符串、文本

 FileField、ImageField——文件、图片

 FilePathField——文件路径

 EmailField——邮件地址

 URLField——URL地址

## 数字（整数）

 IntegerField——整数

 BooleanField——布尔值(1,0)

 SmallIntegerField——整数

 BigIntegerField——整数

 PositiveIntegerField——正整数


## 数字（小数）

 FloatField、DecimalField——小数

## 日期与时间

 DateField——日期

 TimeField——时间

 DateTimeField——日期时间


## 特殊类型

 OneToOneField——一对一关联

```
 ForeignKey——外键关联
```
 ManyToManyField——多对多关联

 GenericForeignKey——复合关联

## 模型基类

```
 django.db.models.Field
```

## 类型之间的关系

```
Field
```
```
DateField
```
```
CharField RelatedField
```
```
FloatField ForeignKey
```
## 类型的选项（可选参数）

- 每个类型都有可选参数，部分类型有必传参数

- 参数传递是无序的（需要写参数的名称）

- 一般情况下，第一个参数不指定名称


## 类型的选项（可选参数）

```
 verbose_name
大多数模型类型的第一个参数
特例：ForeignKey、ManyToManyField 、OneToOneField
方便阅读，即：该字段的含义
```
## 类型的选项（可选参数）

```
 null、blank——是否为Null、空值
```
 db_column——数据库表中对应的字段名称


## 模型代码

- 示例

```
class User(models.Model):
""" 用户模型 """
name = models.CharField('姓名', db_column='username',
max_length=64)
age = models.PositiveIntegerField('年龄', default=0)
```
## 类型的选项（可选参数）

```
 null、blank——是否为Null、空值
```
 db_column——数据库表中对应的字段名称

```
 default——不填写改字段值时的默认值
```
 primary_key、unique——主键、唯一索引


## 类型的选项（可选参数）

```
 help_text——帮助文字
```
```
 choices——可供选择的选项，如：性别的选项（男，女）
 get_FOO_display()——展示choices对应的值
```
## CharField

 max_length——最大长度

```
EmailField——邮件输入
URLField——URL输入
```
- 相关类型


## DateTimeField

 auto_now——更新时间为记录更改时的时间

 auto_now_add——记录创建的时间

```
注意两者之间的区别！
```
#### 了解常见的模型字段类型，才能更好的设计模型

## 小结


## 模型的创建

## 本节内容

- 掌握 模型设计的技巧

- 掌握 ORM模型代码的编写

- 掌握 常见ORM模型字段类型的使用


## 模型的创建

#### 张三

```
姓名
性别
年龄
用户名
```
```
密码
```
```
字段 类型 长度
name varchar 64
sex char 1
age int
username varchar 64
password varchar 256
```
```
注意auto_now和auto_now_add的区别
```
## 小结


#### 怎么同步ORM模型结构到数据库的表？

## 思考

## 模型同步migrate


## 本节内容

- 了解 模型同步的使用场景

- 掌握 模型同步的步骤

- 了解 模型同步的原理

## 实现模型同步

 前提：确认settings.py

```
已将模型添加到INSTALLED_APPS
```

## 实现模型同步

- 步骤一：检查模型是否编写正确

```
>> python manage.py check
```
## 实现模型同步

 步骤二：使用makemigrations生成同步原语

```
>> python manage.py makemigrations
```

## 实现模型同步

 步骤三：使用migrate执行同步

```
>> python manage.py migrate
```
## 模型同步

- 思考：模型变更（添加、修改、删除）了一些字段，怎样快

#### 速的修改数据库对应的表结构？

```
字段 类型 长度
name varchar 64
sex char 1
age int
username varchar 64
password varchar 256
```
```
字段 类型 长度
email varchar 128
```

## 原理解析

 理解migrations

#### 掌握模型同步，再也不用去数据库改字段了

## 小结


#### 我想修改数据库表的名称怎么办？

## 思考

## 模型的元数据


## 本节内容

 掌握 Django中元编程的技巧

 掌握 在模型中配置数据库的表名称

 理解 抽象选项的作用及应用场景

 了解 代理选项的作用及应用场景

## 元数据的描述

```
 使用Meta类来表示
```
```
 对模型的补充说明
```
```
 示例 class Meta:
verbose_name = ' '
verbose_name_plural = ' '
db_table = 'oauth_user'
```

## 元数据的描述

 db_table——模型映射的数据库表的名称

 ordering——指定数据表的默认排序规则

 verbose_name——供编程查看的字段名称（便于阅读）

## 元数据的描述

 abstract——抽象类

```
抽象类不会生成数据库表
```

## 元数据的描述

 proxy——代理模型

```
对父模型的功能进行扩充
```
#### 元数据的设置使ORM的模型的功能更加强大

## 小结


#### 怎样表示ORM模型之间的关系？

## 思考

## 外键关联类型


## 本节内容

- 了解 数据库中常见的关联关系

- 掌握 一对一关系的实现

- 掌握 一对多关系的实现

- 了解 多对多关系的实现

## 关联关系

- 数据库中有哪些关联关系？

####  一对一

####  一对多

####  多对多


## 一对一关系

```
 OneToOneField(to, on_delete, parent_link=False,
**options)
 举例：用户信息进行分表
```
#### 用户基本

#### 信息

#### 用户详细

#### 信息

## 一对多关系

```
 ForeignKey(to, on_delete, **options)
```
 举例：用户提问

```
用户 1 关系 n 问题
```

## 多对多关系

 ManyToManyField(to, **options)

 举例：收藏问题

```
用户 n 关系 n 问题
```
## 类型的参数选项

 to——关联的模型(必传）

```
 模型类
 模型类（字符串）
 self
```

## 类型的参数选项

 on_delete——删除选项(必传）

```
 CASCADE：关联删除
 PROTECT：受保护，不允许被删除
 SET_NULL：设置为None，需要添加选项null=True
```
## 类型的参数选项

 on_delete——删除选项(必传）

```
 SET_DEFAULT：设置为默认值，需要添加选项default
 SET() ：传参设置值
 DO_NOTHING ：什么也不做
```

## 类型的参数选项

 related_name

 related_query_name

#### 是否需要反向引用，反向引用的名称

#### 反向引用的名称

#### 掌握模型中的关联关系是ORM模型设计的

#### 必备技能

## 小结


#### 景点评论、订单评论、旅游攻略评论

#### 如何设计ORM模型精简又便于维护？

## 思考

## 复合类型


## 本节内容

- 理解 复合类型的使用场景

- 掌握 复合类型的模型设计

- 了解 复合类型的设计原理

## 复合类型

- 举例：评论景点、评论订单

```
景点^1 评价 n 评价
```
```
订单 1 评价 n 评价
```
```
想一想：上面需要几张数据库表?
```

## 复合类型

#### 景点信息 订单信息

- 优化后的解决方案

```
评价记录
```
## 复合类型

```
 ContentType——类容模型
```
```
 GenericRelation——反向关联
```
 GenericForeignKey——关联模型

```
 ForeignKey(ContentType)——关联复合模型
```

## 实现评论场景下的复合类型

- 代码实战

## 原理解析

 关键点：ContentType


#### 掌握复合类型的设计使代码更精炼

## 小结

## 章节总结


- ORM模型分析、设计

## 重难点

- ORM模型代码的编写、模型同步

- ORM外键关联、复合类型

## 知识点回顾


## 使用ORM实现CRUD

## 什么是数据库的CRUD

 Create——新增

 Read——读/查询

 Update——修改

 Delete——删除

 一句话概括：数据库的增删改查


- 实现数据修改

- 实现数据新增  实现数据删除

## 章节概要

- 实现简单查询

- 定义模型

```
 了解Django shell的使用技巧
```
## 课程目标

- 掌握新增、批量新增、外键关联数据的新增

- 掌握修改、批量修改、修改时的注意事项

- 掌握简单查询的使用

- 掌握删除功能实现的技巧


## 定义模型

## 本节内容

- 了解 模型的设计方法

- 掌握 模型代码的编写


## 用户模块

- 举例

#### 用户

#### 关系 用户详情

#### 1

#### 1

(^1) 关系 n 登录历史

#### 掌握ORM模型的设计需要多加练习

## 小结


#### 如何使用ORM模型新增数据？

## 思考

## 使用ORM实现数据新增


## 本节内容

```
 了解 Django shell的使用技巧
```
 掌握 使用三种方法新增数据

 掌握 外键关联数据的插入

## Django shell

```
 从控制台（terminal）进入
>> python manage.py shell
 从pyCharm进入
```

## 使用ORM新增数据

```
 使用save()保存数据
```
 使用create()新增数据

```
 使用bulk_create()批量新增数据
```
## 使用save()保存数据

- 示例代码

```
>> user_obj = User(username='admin',
password='password')
>> user_obj.save()
```

## 使用create()新增数据

- 示例代码

```
>> user_obj = User.objects.create(username='admin',
password='password')
>> user_obj.pk
>> 1
```
## 使用bulk_create()批量新增数据

- 示例代码

```
>> user1 = User(username='admin', password='password')
>> user2 = User(username='manager', password='password')
>> user_list = [user1, user2]
>> User.objects.bulk_create(user_list)
```

## 外键关联数据的插入

- 举例：记录用户登录的历史

```
用户 1 关系 n 登录历史
```
## 外键关联数据的插入

- 示例代码

```
>> user = User(username='admin', password='password')
>> LoginHistory.objects.create(user=user, *args, **kwargs)
```

 单条数据插入用create()
 批量数据插入用bulk_create()

## 小结

#### 修改数据的基本逻辑是什么？

## 思考


## 使用ORM实现简单查询

## 本节内容

- 掌握 如何查询单条数据

- 掌握 如何查询多条数据

- 掌握 查询中的编程技巧


## 查询单条数据

```
 get(**kwargs)
按照查询条件返回单条数据
```
 latest(*fields)/earliest(*fields)

```
返回最晚/最早的一条记录
```
## 使用get()查询单条数据

- 示例代码

```
>> User.objects.get(pk=1)
>> user_obj.id
>> 1
```

## 编程技巧

- 注意异常的处理

```
DoesNotExist： 查询的记录不存在
MultipleObjectsReturned：查询的记录有多条
```
## 编程技巧

- 若有则返回，若无则创建后返回

```
object, created = User.objects.get_or_create(*args,
**kwargs)
```

## 编程技巧

- 如果没有则触发 404 异常

```
get_object_or_404(klass, *args, **kwargs)
```
## 查询单条数据

 get(**kwargs)
按照查询条件返回单条数据

 first()/last()

```
返回第一条/最后一条记录
```
 latest(*fields)/earliest(*fields)

```
返回最晚/最早的一条记录
```

## 使用all()查询所有数据

- 示例代码

```
>> User.objects.all()
>> user_obj
>> [<User ...>]
```
## 编程技巧

 打印模型字符串__str__()的使用

```
>> User.objects.all()
>> user_obj
>> [<User :zhangsan>]
```

#### 注意记录不存在/记录有多条时的异常处理

## 小结

#### 有了查询，想想修改数据怎么办？

## 思考


## 使用ORM实现数据修改

## 本节内容

- 理解 数据修改的逻辑

- 掌握 如何修改单条数据

- 掌握 如何批量修改数据及其注意事项

- 了解 批量修改数据时的性能问题及优化


## 使用ORM修改数据

```
 使用save()修改单条数据
 使用update()批量修改数据
```
 使用bulk_update()批量修改数据

## 使用save()修改单条数据

- 示例代码

```
>> user_obj = User.objects.get(pk=1)
>> user_obj.nickname = '管理员'
>> user_obj.save()
```

## 批量修改数据

- 方式一：统一修改

```
SQL示例: UPDATE table SET column='ABC'
```
## 使用update()批量修改数据

- 示例代码

```
>> count = User.objects.all().update(*args, **kwargs)
>> count
>> 3
 注意不能修改外键关联的对象
如： update(user__nickname='李四')
```

## 批量修改数据

- 方式一：统一修改

```
SQL示例: UPDATE table SET column='ABC'
```
 方式二：循环逐一修改

```
for user in user_list
user.nickname='李四’
user.save()
```
## 使用bulk_update()批量修改数据

- 方法使用

```
bulk_update(objs, fields, batch_size=None)
```

## 参数解释

 objs
需要修改的记录列表

 fields
指定需要修改的字段

 batch_size

```
每次提交多少条记录进行修改
```
```
注意update的条件，否则修改了全部数据
```
## 小结


## 使用ORM实现删除操作

## 本节内容

- 掌握 如何删除单条数据

- 掌握 如何批量删除数据

- 了解 物理删除和逻辑删除的实现及其区别


## 使用ORM物理删除数据

 使用模型的delete()删除数据

```
>> user_obj = User.objects.get(pk=1)
>> user_obj.delete()
```
```
>> User.objects.all().delete()
```
#### 删除单条数据

#### 删除多条数据（批量删除）

## 使用ORM逻辑删除数据

 回顾：Flask中如何实现的逻辑删除？


## 使用ORM删除数据

- 面试题：什么是逻辑删除？什么是物理删除？

- 思考：逻辑删除与物理删除的区别

## 逻辑删除vs物理删除

- 物理删除 ^ 逻辑删除

#### 将数据从数据库干掉

#### 干掉了就不占磁盘空间了

#### 将数据标记删除

#### 删除后还占磁盘空间

#### 干掉了就找不回来了 删除后还可以恢复

#### 删除后通过查询条件不展示给用户


#### 使用逻辑删除是数据保护的一个技巧

## 小结

## 课程总结


## 知识点总结

- 涉及多表新增/修改/删除时要注意数据的一致性

- 批量修改和删除时要注意查询条件

- 实际工作中会考虑到性能问题，要不断积累

## 经验及技巧

- CRUD时要注意异常的处理


#### 复杂的查询怎么实现？

## 思考

## 查询条件的使用


- 查询优化

- 分页处理  聚合与统计

## 章节概要

- 查询条件

 结果集QuerySet

- 按多个条件查询

- 数据的一致性

- 事务处理

 了解QuerySet的常用方法

## 课程目标

 掌握在Django中使用分页

```
 了解常见的查询条件及其使用方法
```
 掌握使用多个查询条件进行按条件查询


## 课程目标

- 掌握聚合与统计的原理及其使用

- 掌握F函数保证数据一致性的使用及其原理

```
 掌握Django中事务的处理
```
- 了解查询的优化、调试技巧

## 结果集QuerySet


## 本节内容

- 了解 结果集中常用的一些方法

- 了解 结果集中链式查询的技巧

- 理解 模型管理器、自定义模型管理器

- 掌握 基本的调试技巧

## 结果集QuerySet

```
 QuerySet表示从数据库中取出来的对象的集合
 它可以含有零个、一个或者多个过滤器(filter)
 从模型的Manager那里取得QuerySet
 QuerySet的筛选结果本身还是QuerySet
 QuerySet是惰性的
```

## QuerySet常用方法

- 数据插入/修改/删除

- 查询单条记录

- 条件判断/统计

- 链式查询

## 插入数据

```
 create()——创建/新增一条数据库记录
```
```
 get_or_create()——有则返回，没有则创建记录
```
```
 bulk_create()——创建/新增多条数据库记录
```

```
 update()——修改记录
 delete()——物理删除记录
```
## 修改和删除

## 返回单条数据

```
 get()——返回单条记录
```
 get_or_create()——有则返回，没有则创建记录

```
 first()/last()——返回第一条/最后一条记录
 latest()/earliest()——返回最晚/最早的一条记录
```

 count()

```
返回记录的行数之和
```
## 总记录数

 exists()

## 条件判断

#### 结果集是否存在（是否存在 1 条以上的记录）


## 链式查询方法

 all()——查询所有记录

 none()——创建一个空的结果集

 using()——使用指定的数据库查询(多数据库支持)

## 链式查询方法

 exclude()——排除满足条件的多条记录

 filter()——筛选出满足条件的多条记录

 order_by()——对查询的记录排序


## 模型管理器(Manager)

```
 Manager是Django的模型进行数据库查询操作的接口
```
```
 每个模型都拥有至少一个Manager
```
 Django 为每个模型类添加一个名为objects的默认Manager

## 自定义管理器

- 添加新的管理器

```
from django.db import models
class User(models.Model):
#...
users = models.Manager()
```

## 调试技巧

```
print(QuerySet.query)
```
- 打印查询执行的SQL语句

- IDE工具的查询调试

```
 objects是默认的模型管理器对象
 掌握调试技巧会让你事半功倍
```
## 小结


#### ORM中的查询条件是怎么使用的？

## 思考

## 查询条件


## 本节内容

- 了解 查询条件的语法

- 掌握 常见的查询条件的使用

- 掌握 外键关联的查询条件使用

## 查询条件语法

- 查询语法

#### 字段名称__查询条件=“查询内容” 注意是双下划线

```
如：id__exact=6
```

## 查询条件

- 相等/等于/布尔条件

- 是否包含**字符串

- 以**开始/结束

- 日期及时间

- 外键关联

## 相等/等于

 exact——等于**值(默认的形式)

```
如：id__exact=6 或者 id=6
 iexact——像**值
如：name__iexact=‘zhangsan’
# name LIKE ‘zhangsan’
```

## 布尔条件

 gt——大于某个值

 gte——大于或等于某个值

 lt——小于某个值

 lte——小于或等于某个值

 isnull——是否为空值

## 是否包含**字符串

 contains——包含**值

```
如：name__contains=‘san’
 icontains——包含**值，不区分大小写
如：name__contains=‘san’
# ZhangSan zhangsan 都满足条件
```

## 是否包含**字符串

- 在**选项(列表)之内

```
in
```
## 以**开始/结束

- 以**开始

```
startswith、istartswith
```
 以**结束
endswith、iendswith


## 日期及时间

```
 date——日期
 year——年
 month——月份
 day——天
 hour/minute/second——时分秒
```
 week/week_day——星期

## 外键关联查询

- 查询在线问答系统中某个用户的回答

```
filter(user__username='张三'）
```

#### 掌握__的查询语法

## 小结

#### OR（或）的查询如何实现？

## 思考


## 按多个条件查询

## 本节内容

- 了解 同时查询多个条件的使用

- 掌握 多个查询条件同时满足的查询

- 掌握 多个查询条件部分满足的查询


## 多个条件同时满足

 filter的深入使用

```
 &运算符的使用
 Q函数的使用
```
## filter的深入使用

- 示例

```
filter(name__icontains='张', status=1)
```

## &运算符

- 示例

```
user_list = User.objects.filter(name__icontains='张) &
User.objects.filter(status=1)
```
## Q()函数

- 使用Q()函数实现复杂的查询

- Q()函数支持&（且）和|（或），对应SQL中的AND和OR


## 且（AND）

- 示例：慕课网的课程搜索

## 或（OR）

- 示例场景

```
搜索Vue和Python相关的课程
```

#### Q函数可以实现多个查询的条件的复杂场景

## 小结

#### 外键查询有没有查询性能问题？

## 思考


## 查询优化

## 本节内容

- 了解 查询优化的分析技巧，调试工具的使用

- 掌握 查询条件的优化技巧

- 了解 SQL查询的使用


## 查询分析

- 哪些查询比较慢？

- 到底执行了多少次查询？

## 分析技巧

 技巧一：QuerySet. query属性查看执行的SQL

```
>>> q = User.objects.all()
>>> print(q.query)
>>> SELECT ...
```

## 分析技巧

```
>> pip install django-debug-toolbar
```
```
 方技巧二：django-debug-toolbar
```
## 优化外键关联查询

 QuerySet. select_related()

```
将外键关联的对象查询合并到主查询，一次性查询结果，减少
SQL执行的数量
```

## 使用SQL查询

```
 方式一：使用管理器的raw(sql)函数
```
```
返回django.db.models.query.RawQuerySet实例
```
```
raw(raw_query, params=None, translations=None)
```
## 使用SQL查询

####  获取数据库连接

####  从连接得到游标

 方式二：获取数据库连接、游标，直接执行sql

```
from django.db import connection
```
```
cursor = connection.cursor()
```

## 使用SQL查询

####  执行SQL

####  查询结果

 方式二：获取数据库连接、游标，直接执行sql

```
cursor.execute("SELECT * FROM table WHERE baz=%s", [baz])
```
```
row = cursor.fetchone()
```
#### 性能的优化可以从多个方面着手

## 小结


在Django中如何使用分页解决慢查询？

## 思考

## 分页处理


## 本节内容

- 了解 为什么要进行分页查询

- 了解 使用分片实现分页

 掌握 使用Paginator实现分页

## 分页处理

- 为什么需要对查询的数据进行分页处理？

#### 分页前 分页后


## 分页的实现

```
 方式一：对查询结果集QuerySet进行分片
 方式二：使用django.core.paginator进行分页处理
```
## 分片分页处理

 返回前n个对象（LIMIT 5 ）
User.objects.all()[:5]

- 返回第 6 到第 10 个对象（OFFSET 5 LIMIT 5 ）

```
User.objects.all()[5:10]
```

## 使用Paginator进行分页

 步骤一：取得分页器 Paginator(objects, page_size)

 步骤二：取得页面实例page = p.page(page_num)

## 分页处理相关类

 Paginator——分页器

```
 Page——某一页对象
```
 异常
 InvalidPage —— 无效的页码
 PageNotAnInteger —— 页码必须是整数
 EmptyPage —— 空页（没有数据）


## 使用Paginator进行分页

 步骤一：取得分页器 Paginator(objects, page_size)

```
>> p = Paginator(objects, 2)
```
```
 objects —— 要进行分页的数据
 page_size —— 每页的数据多少
```
## Paginator的属性

```
 count —— 数据记录的总条数
```
```
 num_pages —— 总页数（总记录条数 / 每页大小）
```
```
 page_range —— 页码范围
```

## 使用Paginator进行分页

```
 步骤二：取得页面实例page = p.page(page_num)
```
```
>> p = Paginator(objects, 2)
>> page = p.page(3)
```
```
page_num —— 当前页的页码，如第几页
```
## 页面实例的属性

```
 number —— 当前页的页码
```
 object_list —— 当前页的数据列表

 paginator —— 分页器对象的引用


## 页面实例的常用方法

 has _next（） —— 是否还有下一页

 has_previous（） —— 是否还有上一页

```
 next_page_number（） —— 下一页的页码，如果没有，触发
InvalidPage异常
 next_page_number（） —— 上一页的页码，如果没有，触发
InvalidPage异常
```
```
 has_other_pages（） —— 是否还有其他页（上/下一页）
```
```
掌握使用Paginator进行分页处理
（使用分页也是查询优化的一部分）
```
## 小结


#### 查询中的数据统计如何实现？

## 思考

## 聚合及统计


## 本节内容

- 了解 聚合统计的使用场景

- 掌握 聚合函数的使用

- 掌握 统计函数的使用

## 内置聚合函数

 Sum——求和

 Avg——求平均数

 Count——计数

```
 Max/Min——最大值/最小值
```

## 实现数据统计

```
 使用aggregate从整个查询结果集生成统计数据
 示例
>>> from django.db.models import Avg
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}
```
## 数据统计练习

- 练习 1 ：求某个学生期末成绩的总和

- 练习 2 ：求某一科目成绩的最高分/最低分

- 练习 3 ：求某一科目成绩的平均分


## 实现聚合查询

###  使用annotate为查询结果集中的每一项生成统计数据

- 示例

```
>>> from django.db.models import Count
>>> q = Book.objects.annotate(Count('authors'))
>>> q[0]
<Book: The Definitive Guide to Django>
>>> q[0].authors__count
```
## 聚合查询练习

- 练习：求每个学生期末成绩的总和


 aggregate返回一个结果
 annotate在列表中使用（返回多个结果）

## 小结

#### 修改用户的剩余积分如何保证数据的准确？

## 思考


## 数据的一致性

## 本节内容

```
 了解 web应用的场景（多线程）
 了解 多线程下的并发的场景
 掌握 F函数的使用
```
 理解 F函数与save(）的区别


## 数据是否一致

```
 如下场景中，各执行 100 次，最终a=200?
```
```
a+=1 a=0 a+=1
```
```
数据库
```
## F()函数的使用

- F()函数从数据库操作层面修改数据

- F()函数可避免同时操作时竞态条件


```
注意F函数和save方法的区别
```
## 小结

#### 如果是同时操作多个表怎么保证数据的一致性？

## 思考


## 事务处理

## 本节内容

- 了解 什么是事务

 了解 Django中事务控制的原理

 掌握 在Django中自动提交事务

```
 掌握 在Django中手动控制事务
```

## 什么是事务

- 一个程序执行单元

- 四个特性：ACID

## 事务的特性

 原子性（atomicity）
一个事务是一个不可分割的工作单位，事务中包括的操作
要么都做，要么都不做。

 一致性（consistency）
事务必须是使数据库从一个一致性状态变到另一个一致性
状态。


## 事务的特性

 隔离性（isolation）

```
一个事务的执行不能被其他事务干扰
```
 持久性（durability）

```
指一个事务一旦提交，它对数据库中数据的改变就应该是
永久性的。
接下来的其他操作或故障不应该对其有任何影响。
```
## 什么是事务

- 举例：银行转账

```
张三
1000
```
```
李四
500
```
```
转 100 元
```
####  张三账户减 100 元

####  张三账户剩余 900 元

####  李四账户加 100 元

####  李四账户剩余 600 元


## 事务与回滚

- 事务：多个数据库逻辑操作的集合

- 回滚：多个逻辑中某个操作出错，回到初始状态

- 事务的原子性要求事务要么全部完成，要么全部不完成，不

#### 可能停滞在某个中间状态

## 使用场景

- 对多个ORM模型操作时

- 对结果要求严格一致（要么成功，要么失败）


## 在django中使用事务

- 方式一：自动提交

####  使用装饰器

```
 使用with语法
 方式二：手动提交和回滚
```
## 自动提交事务

- 示例代码

```
from django.db import transaction
@transaction.atomic
def viewfunc(request):
# 事务内的代码
do_stuff()
```

## 过程解析

```
 进入到最外层的 atomic 代码块时会打开一个事务
 进入到内层atomic代码块时会创建一个标记
 退出内部块时会释放或回滚至标记
 退出外部块时提交或回退事务
```
## 手动提交事务

- 示例代码

```
from django.db import transaction
try:
a.save()
b.save()
transaction.commit() # 提交事务
except:
transaction.rollback() # 回滚
```

#### 理解事务的ACID

## 小结

## 课程总结


- 分页处理、模板的使用

- 查询条件，多条件查询

- 聚合统计

## 重难点

- 查询优化及调试技巧

- 事务的处理

## 知识点回顾


