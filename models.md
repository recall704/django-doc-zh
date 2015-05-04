A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

The basics:

* Each model is a Python class that subclasses [django.db.models.Model](https://docs.djangoproject.com/en/1.8/ref/models/instances/#django.db.models.Model).
* Each attribute of the model represents a database field.
* With all of this, Django gives you an automatically-generated database-access API; see [Making queries](https://docs.djangoproject.com/en/1.8/topics/db/queries/).
*************************************
模型是一个独立的、信息明确的数据信息。它包含了你存储的数据的必要字段和行为。通常一个模型对应一个 数据库的 一张表 （table）。

基础：

*   每个模型都是 [django.db.models.Model](https://docs.djangoproject.com/en/1.8/ref/models/instances/#django.db.models.Model) 类的子类

*   模型的每个属性对应表的一个字段.

*   在这基础上，django 自动生成了访问数据的API，详细内容请参考 [查询](https://docs.djangoproject.com/en/1.8/topics/db/queries/)

*************************************
#### Quick example

This example model defines a Person, which has a first_name and last_name:
```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
first_name and last_name are fields of the model. Each field is specified as a class attribute, and each attribute maps to a database column.

The above Person model would create a database table like this:
```sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
Some technical notes:

* The name of the table, myapp_person, is automatically derived from some model metadata but can be overridden. See Table names for more details.
* An id field is added automatically, but this behavior can be overridden. See Automatic primary key fields.
* The CREATE TABLE SQL in this example is formatted using PostgreSQL syntax, but it’s worth noting Django uses SQL tailored to the database backend specified in your settings file.
*************************************
#### 一个简单的栗子
下面的例子是一个包含了 first_name 和 last_name 的名为 Person 的模型。
```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
first_name 和 last_name 是模型的字段，每个字段作为类的一个属性，每个属性对应数据库中(表的)一列。

上面的模型将会创建一个如下的数据库表:
```sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
注意：

* 表的名字 myapp_person，是由模型的元数据指定的，你可以自定义该选项。详情请参考[表名](https://docs.djangoproject.com/en/1.8/ref/models/options/#table-names)

* 模型会自动添加一个名为 id 的主键，同样你也可以自定义主键。详情请参考[主键字段](https://docs.djangoproject.com/en/1.8/topics/db/models/#automatic-primary-key-fields)

* 上面的 SQL 对应的是 PostgreSQL 的语法，值得注意的是，你需要在 [配置文件](https://docs.djangoproject.com/en/1.8/topics/settings/) 中指定你所使用的数据库后端。
*************************************
#### Using models

Once you have defined your models, you need to tell Django you’re going to use those models. Do this by editing your settings file and changing the INSTALLED_APPS setting to add the name of the module that contains your models.py.

For example, if the models for your application live in the module myapp.models (the package structure that is created for an application by the manage.py startapp script), INSTALLED_APPS should read, in part:
```python
INSTALLED_APPS = (
    #...
    'myapp',
    #...
)
```
When you add new apps to INSTALLED_APPS, be sure to run manage.py migrate, optionally making migrations for them first with manage.py makemigrations.
*************************************
#### 使用模型
一旦你定义好了模型，你需要告诉Django 你将要使用该模型。你需要编辑 `配置文件`，在 `INSTALLED_APPS` 中添加包含 models.py 文件的 app 模块的名字。

举个栗子，你的模型定义在 myapp.models 模块中（app 的包结构是由 `manage.py startapp` 这个脚本创建的），你应该修改配置文件大概变成下面这个样子：
```python
INSTALLED_APPS = (
    #...
    'myapp',
    #...
)
```
将新的 app 添加到配置文件之后，你需要执行命令
```bash
python manage.py migrate
```
来创建数据库表，在这之前，你也可以执行
```bash
python manage.py makemigrations
```
生成迁移文件。

> 译者注：
> 在django 1.7 之前（不包括1.7），你需要执行如下命令
> ```
> python manage.py syncdb
> ```
> 1.7 版本之前，你的模型修改执行 syncdb 并不会同步到数据库，你可能需要 south 等之类的第三方库来同步你的数据库。

*************************************
#### Fields
The most important part of a model – and the only required part of a model – is the list of database fields it defines. Fields are specified by class attributes. Be careful not to choose field names that conflict with the models API like clean, save, or delete.

Example:
```python
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```
*************************************
#### 字段
模型中最重要的部分，也是不可缺少的部分，就是模型的字段。
字段是类的属性。需要特别注意的是，你不能使用[模型的 API](https://docs.djangoproject.com/en/1.8/ref/models/instances/) 的名字来做为字段的名字。
比如 clean、save 或者 delete。

例子：
```python
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```
*************************************
#### Field types

Each field in your model should be an instance of the appropriate Field class. Django uses the field class types to determine a few things:

* The database column type (e.g. INTEGER, VARCHAR).
* The default HTML widget to use when rendering a form field (e.g. `<input type="text">, <select>`).
* The minimal validation requirements, used in Django’s admin and in automatically-generated forms.

Django ships with dozens of built-in field types; you can find the complete list in the model field reference. You can easily write your own fields if Django’s built-in ones don’t do the trick; see Writing custom model fields.
*************************************
#### 字段类型
模型中的每个字段都是对应 Field 类的一个实例。字段类的不同，决定了：

* 数据库列的类型 (e.g. INTEGER, VARCHAR)

* 作为表单返回时显示的 html 控件。

* 在Django的 admin 中和 自动生成的表单中，完成最基本的表单验证。

Django 包含了一系列的 字段类型，你可以参考[完整字段列表](https://docs.djangoproject.com/en/1.8/ref/models/fields/#model-field-types)。不需要什么特别的技巧，你也可以自定义自己的字段；自定义模型字段请参考 [自定义模型字段](https://docs.djangoproject.com/en/1.8/howto/custom-model-fields/)。
*************************************
#### Field options

#### 字段选项
Each field takes a certain set of field-specific arguments (documented in the model field reference). For example, CharField (and its subclasses) require a max_length argument which specifies the size of the VARCHAR database field used to store the data.

每个字段包含一系列字段特有的参数([模型字段参考](https://docs.djangoproject.com/en/1.8/ref/models/fields/#model-field-types))，比如，CharField （包含其子类）对应的字段必须要指定 max_length 参数，这个参数确定了数据库中 VARCHAR 的长度。

There’s also a set of common arguments available to all field types. All are optional. They’re fully explained in the reference, but here’s a quick summary of the most often-used ones:

同样的，字段也有一些通用的参数，它们都是可选的，下面是常用的参数的解释说明：

`null`
If True, Django will store empty values as NULL in the database. Default is False.

如果该选项为 True ，Django将会存储空字符串在数据库中。默认是 False。

`blank`
If True, the field is allowed to be blank. Default is False.
如果为 True，该字段允许为空，默认是 False。

Note that this is different than null. null is purely database-related, whereas blank is validation-related. If a field has blank=True, form validation will allow entry of an empty value. If a field has blank=False, the field will be required.

需要注意的是，null 和 blank 是不同的，null 和数据库有关，而 blank 和表单验证有关。如果 blank = True ，则表单允许该选项为空，如果 blank=False ，该选项是必填项。

`choices`
An iterable (e.g., a list or tuple) of 2-tuples to use as choices for this field. If this is given, the default form widget will be a select box instead of the standard text field and will limit choices to the choices given.

choices 是一个可迭代的二维 元组，如果给定了该选项，html 表单控件会返回的是 `下拉列表框`，用户只能在给定的选项中进行选择。

A choices list looks like this:
一个 choices 大概是下面这个样子：
```python
YEAR_IN_SCHOOL_CHOICES = (
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
)
```
The first element in each tuple is the value that will be stored in the database, the second element will be displayed by the default form widget or in a ModelChoiceField. Given an instance of a model object, the display value for a choices field can be accessed using the get_FOO_display method. For example:

元组中的第一列元素将会存储在数据库中，第二列将会作为 html 显示。在获取数据时，如果要获取第一列的数据，直接使用 Model.fieldname 就能访问到数据；如果要获得第二列的数据，你需要使用 get_FOO_display 这样的函数方法来进行访问，比如：
```python
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
>>> p = Person(name="Fred Flintstone", shirt_size="L")
>>> p.save()
>>> p.shirt_size
'L'
>>> p.get_shirt_size_display()
'Large'
```

`default`
The default value for the field. This can be a value or a callable object. If callable it will be called every time a new object is created.

指定字段的默认值，可以是一个值，也可以是一个可调用的对象，如果是可调用的对象，每当一个新对象创建的时候，都会被调用一次。

`help_text`
Extra “help” text to be displayed with the form widget. It’s useful for documentation even if your field isn’t used on a form.

显示在表单上的帮助信息。即使不在表单上显示，也可以作为form 的文档。

`primary_key`
If True, this field is the primary key for the model.
如果为真，这个字段将会作为 `主键`

If you don’t specify primary_key=True for any fields in your model, Django will automatically add an IntegerField to hold the primary key, so you don’t need to set primary_key=True on any of your fields unless you want to override the default primary-key behavior. For more, see Automatic primary key fields.

如果你不在任何字段指定 primary_key=True，Django 将会添加一个 IntegerField 类型的字段作为主键，所以你不必在任何字段上设置 primary_key=True，当然了，你也可以在某个字段上设置该选项，详情请参考 [主键字段](https://docs.djangoproject.com/en/1.8/topics/db/models/#automatic-primary-key-fields)

The primary key field is read-only. If you change the value of the primary key on an existing object and then save it, a new object will be created alongside the old one. For example:

主键字段是只读的。如果你在已存在的对象上修改主键然后保存，这将会创建一个和已有对象相同的新对象。
例如：
```python
from django.db import models

class Fruit(models.Model):
    name = models.CharField(max_length=100, primary_key=True)
>>> fruit = Fruit.objects.create(name='Apple')
>>> fruit.name = 'Pear'
>>> fruit.save()
>>> Fruit.objects.values_list('name', flat=True)
['Apple', 'Pear']
```
`unique`
If True, this field must be unique throughout the table.

Again, these are just short descriptions of the most common field options. Full details can be found in the common model field option reference.

如果该选项为真，在整个表中，该字段对应的值必须是唯一的（译者注：即不能有相同的数据，比如说用户名）

同样的，这里只是一个简短的描述，详细内容请参考 [通用模型字段选项参考](https://docs.djangoproject.com/en/1.8/ref/models/fields/#common-model-field-options)
*************************************
#### Automatic primary key fields

By default, Django gives each model the following field:
```python
id = models.AutoField(primary_key=True)
```
This is an auto-incrementing primary key.

If you’d like to specify a custom primary key, just specify primary_key=True on one of your fields. If Django sees you’ve explicitly set Field.primary_key, it won’t add the automatic id column.

Each model requires exactly one field to have primary_key=True (either explicitly declared or automatically added).
*************************************
#### 主键
默认情况下，Django会给每个模型添加如下一个字段
```python
id = models.AutoField(primary_key=True)
```
这是一个自增长的主键。
如果你想指定一个自定义的字段作为主键，只需要给该字段设置选项 primary_key=True，一旦你显示的指定了某个字段作为主键，Django将不会再添加 id 这个字段（作为主键）

每个模型都需要一个主键，不管是自定义的还是Django默认给我们设置的。
*************************************
#### Verbose field names
Each field type, except for ForeignKey, ManyToManyField and OneToOneField, takes an optional first positional argument – a verbose name. If the verbose name isn’t given, Django will automatically create it using the field’s attribute name, converting underscores to spaces.

In this example, the verbose name is "person's first name":
```python
first_name = models.CharField("person's first name", max_length=30)
```
In this example, the verbose name is "first name":
```python
first_name = models.CharField(max_length=30)
```
ForeignKey, ManyToManyField and OneToOneField require the first argument to be a model class, so use the verbose_name keyword argument:
```python
poll = models.ForeignKey(Poll, verbose_name="the related poll")
sites = models.ManyToManyField(Site, verbose_name="list of sites")
place = models.OneToOneField(Place, verbose_name="related place")
```
The convention is not to capitalize the first letter of the verbose_name. Django will automatically capitalize the first letter where it needs to.
*************************************
#### 字段详细名称