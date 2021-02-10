# Django学习笔记（二）-配置数据库、创建模型

## 1、数据库配置

在创建的工程项目中找到settings.py文件，该文件可以用来配置使用的数据库，django模式使用自带的SQLite。

现在将默认的数据库改为MySQL

注意：需要先在MySQL中创建需要使用的库，如使用命令创建一个库CREATE DATABASE djangodemo; 

~~~python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'djangodemo',
        'USER':'xxx',
        'PASSWORD':'xxxxxx',
        'HOST': 'xxxxxxxxxx',
        'PORT':'3306'
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
~~~

 ## 2、创建数据库表

1、创建数据库表

2、数据库迁移

3、 `migrate` 命令只会为在 `INSTALLED_APPS`里声明了的应用进行数据库迁移。 

~~~shell
#创建命令：python manage.py migrate
E:\PyProject\Development\djangodemo>python manage.py migrate
System check identified some issues:

WARNINGS:
?: (mysql.W002) MySQL Strict Mode is not set for database connection 'default'
        HINT: MySQL's Strict Mode fixes many data integrity problems in MySQL, such as data truncation upon insertion, by escalating warnings into
 errors. It is strongly recommended you activate it. See: https://docs.djangoproject.com/en/2.2/ref/databases/#mysql-sql-mode
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
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

~~~

## 3、创建模型

在 Django 中写一个数据库驱动的 Web 应用的第一步就是定义模型 - 也就是数据库结构设计和附加的其它元数据。  

- 模型

模型是真实数据的简单明确的描述。它包含了储存的数据所必要的字段和行为。 

下面用官网的投票应用创建两个模型：问题Question模型和选项Choice模型，这些模型在投票应用目录中的models.py文件中编写。

models.Model创建表的类型参数

~~~python
类型
1、models.AutoField　　自增列 = int(11)
　　如果没有的话，默认会生成一个名称为 id 的列，如果要显示的自定义一个自增列，必须将给列设置为主键 primary_key=True。
2、models.CharField　　字符串字段
　　必须 max_length 参数
3、models.BooleanField　　布尔类型=tinyint(1)
　　不能为空，Blank=True
4、models.ComaSeparatedIntegerField　　用逗号分割的数字=varchar
　　继承CharField，所以必须 max_lenght 参数
5、models.DateField　　日期类型 date
　　对于参数，auto_now = True 则每次更新都会更新这个时间；auto_now_add 则只是第一次创建添加，之后的更新不再改变。
6、models.DateTimeField　　日期类型 datetime
　　同DateField的参数
7、models.Decimal　　十进制小数类型 = decimal
　　必须指定整数位max_digits和小数位decimal_places
8、models.EmailField　　字符串类型（正则表达式邮箱） =varchar
　　对字符串进行正则表达式
9、models.FloatField　　浮点类型 = double
10、models.IntegerField　　整形
11、models.BigIntegerField　　长整形
　　integer_field_ranges = {
　　　　'SmallIntegerField': (-32768, 32767),
　　　　'IntegerField': (-2147483648, 2147483647),
　　　　'BigIntegerField': (-9223372036854775808, 9223372036854775807),
　　　　'PositiveSmallIntegerField': (0, 32767),
　　　　'PositiveIntegerField': (0, 2147483647),
　　}
12、models.IPAddressField　　字符串类型（ip4正则表达式）
13、models.GenericIPAddressField　　字符串类型（ip4和ip6是可选的）
　　参数protocol可以是：both、ipv4、ipv6
　　验证时，会根据设置报错
14、models.NullBooleanField　　允许为空的布尔类型
15、models.PositiveIntegerFiel　　正Integer
16、models.PositiveSmallIntegerField　　正smallInteger
17、models.SlugField　　减号、下划线、字母、数字
18、models.SmallIntegerField　　数字
　　数据库中的字段有：tinyint、smallint、int、bigint
19、models.TextField　　字符串=longtext
20、models.TimeField　　时间 HH:MM[:ss[.uuuuuu]]
21、models.URLField　　字符串，地址正则表达式
22、models.BinaryField　　二进制
23、models.ImageField   图片
24、models.FilePathField 文件
参数
1、null=True
　　数据库中字段是否可以为空
2、blank=True
　　django的 Admin 中添加数据时是否可允许空值
3、primary_key = False
　　主键，对AutoField设置主键后，就会代替原来的自增 id 列
4、auto_now 和 auto_now_add
　　auto_now   自动创建---无论添加或修改，都是当前操作的时间
　　auto_now_add  自动创建---永远是创建时的时间
5、choices
GENDER_CHOICE = (
        (u'M', u'Male'),
        (u'F', u'Female'),
    )
gender = models.CharField(max_length=2,choices = GENDER_CHOICE)
6、max_length
7、default　　默认值
8、verbose_name　　Admin中字段的显示名称
9、name|db_column　　数据库中的字段名称
10、unique=True　　不允许重复
11、db_index = True　　数据库索引
12、editable=True　　在Admin里是否可编辑
13、error_messages=None　　错误提示
14、auto_created=False　　自动创建
15、help_text　　在Admin中提示帮助信息
16、validators=[]
17、upload-to

~~~

创建模型

~~~python
#官网例子小改
from django.db import models

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    type = models.CharField(max_length=1)
    update_time = models.DateField(auto_now=True,name='最后更新时间')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    update_time = models.DateField(auto_now=True,name='最后更新时间')
~~~

## 4、激活模型

激活模型就是将创建的模型生成到数据库表中。

首先需要将创建的投票应用加入到 INSTALLED_APPS中

~~~python

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls.apps.PollsConfig',  
    #因为 PollsConfig 类写在文件 polls/apps.py 中，所以它的点式路径是 'polls.apps.PollsConfig'
]
~~~

执行命令检查模型文件是否有修改，用于后续数据表的创建和迁移准备

~~~shell
python manage.py makemigrations polls
#输出
E:\PyProject\Development\djangodemo>python manage.py makemigrations polls
Migrations for 'polls':
  polls\migrations\0001_initial.py
    - Create model Question
    - Create model Choice

~~~

执行`python manage.py makemigrations polls`命令后会先生成需要执行的sql语句，可以使用`python manage.py sqlmigrate polls 0001`命令来查看`0001`是生成的文件名部分，文件存放在`polls/migrations/ `目录

 `sqlmigrate`命令并没有真正在你的数据库中的执行迁移 - 它只是把命令输出到屏幕上，让你看看 

~~~sql
#查看生成的文件
E:\PyProject\Development\djangodemo>python manage.py sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE `polls_question` (`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY, `question_text` varchar(200) NOT NULL, `pub_date` datetime(6) NOT
 NULL, `type` varchar(1) NOT NULL, `最后更新时间` date NOT NULL);
--
-- Create model Choice
--
CREATE TABLE `polls_choice` (`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY, `choice_text` varchar(200) NOT NULL, `votes` integer NOT NULL, `最
后更新时间` date NOT NULL, `question_id` integer NOT NULL);
ALTER TABLE `polls_choice` ADD CONSTRAINT `polls_choice_question_id_c5b4b260_fk_polls_question_id` FOREIGN KEY (`question_id`) REFERENCES `polls_q
uestion` (`id`);
COMMIT;

~~~

再次执行`python manage.py migrate `命令时，根据最新模型在数据库中创建数据表

~~~shell
E:\PyProject\Development\djangodemo>python manage.py migrate
System check identified some issues:

WARNINGS:
?: (mysql.W002) MySQL Strict Mode is not set for database connection 'default'
        HINT: MySQL's Strict Mode fixes many data integrity problems in MySQL, such as data truncation upon insertion, by escalating warnings into
 errors. It is strongly recommended you activate it. See: https://docs.djangoproject.com/en/2.2/ref/databases/#mysql-sql-mode
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK

~~~

![](E:\学习\django\第二篇\1.png)

## 5、本篇总结

在django中改变模型的三个步骤为：

- 编辑models.py文件，改变模型

- 运行python manage.py makemigrations 为模型生成变更的数据迁移文件

- 运行python manage.py manage.py migrate 来执行数据迁移

