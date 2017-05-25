# 教程02
本教程上接 [教程第1部分](./tutorial01.md) 。我们将设置数据库，创建第一个模型，并快速了解一下Django自动生成的管理网站。

## 数据库设置

现在，打开 `mysite/settings.py` 。这是一个标准的Python模块，包含了代表 Django 设置的模块级变量。

默认的配置使用SQLite数据库，如果你是数据库的新手，或者你仅仅只对尝试Django感兴趣，这是最简单的选择。Python自带了SQLite数据库，所有你不需要安装其他的任何东西来支持你的数据库。然而当开始你的第一个正式项目时，你可能想要使用一个扩展性更强的数据库，比如PostgreSQL，来避免切换数据库的头痛。

如果你想要使用其他的数据库，安装合适的 [数据库绑定](https://docs.djangoproject.com/en/1.11/topics/install/#database-installation) ，然后更改 [DATABASES](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-DATABASES) 中 `'default'` 下的以下键的值，以匹配你的数据库连接设置。

-  [ENGINE](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-DATABASE-ENGINE) – 从 `'django.db.backends.sqlite3'` , `'django.db.backends.postgresql'` , `'django.db.backends.mysql'` , `'django.db.backends.oracle'` 中选一个，至于其他请查看 [also available](https://docs.djangoproject.com/en/1.11/ref/databases/#third-party-notes) .
- NAME – 你的数据库名。如果你使用 SQLite，该数据库将是你计算机上的一个文件；在这种情况下， [NAME](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-NAME) 将是一个完整的绝对路径，而且还包含该文件的名称。默认值 `os.path.join(BASE_DIR, 'db.sqlite3')` 会把文件保存在你的项目文件夹里。

如果你没有使用SQLite作为你的数据库，则像 [USER](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-USER) ， [PASSWORD](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-PASSWORD) 和 [HOST](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-HOST) 这些额外设置是必须添加的，有关详细信息，请参阅 [DATABASES](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-DATABASES) 的参考文档。

>  **对于不是SQLite的数据库** 

> 如果你使用的数据库不是SQLite，请确保这时你已经创建了一个数据库。在你的数据库交互控制台中使用“ `CREATE DATABASE database_name;` ”来创建。同时还要确认在 `mysite/settings.py` 中指定的数据库用户具有创建数据库的权限，这将允许自动创建后续教程中需要的 [测试数据库](https://docs.djangoproject.com/en/1.11/topics/testing/overview/#the-test-database) 。

> 如果你使用 SQLite ，你不需要事先创建任何东西 - 数据库文件在需要的时候将会自动创建。

当你编辑 `mysite/settings.py` 时，将 [TIME_ZONE](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-TIME_ZONE) 修改为你所在的时区。

同时，注意文件底部的 [INSTALLED_APPS](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-INSTALLED_APPS) 设置。它保存了当前 Django实例已激活的所有 Django 应用。每个应用可以被多个项目使用，而且你可以打包和分发给其他人在他们的项目中使用。

默认情况下，INSTALLED_APPS 包含以下应用，这些都是由 Django 提供的：

- django.contrib.auth – 身份验证系统。
- django.contrib.contenttypes – 内容类型框架。
- django.contrib.sessions – session 框架。
- django.contrib.sites – 网站管理框架。
- django.contrib.messages – 消息框架。
- django.contrib.staticfiles – 静态文件管理框架。

这些应用在一般情况下是默认包含的。

所有这些应用中每个应用至少使用一个数据库表，所以在使用它们之前我们需要创建数据库中的表。要做到这一点，请运行以下命令：

	$ python manage.py migrate

migrate 命令参照 INSTALLED_APPS 设置，并在你的 `mysite/settings.py ` 文件所配置的数据库中创建必要的数据库表，并且数据库跟随应用程序迁移（稍后将介绍）。你会看到于每个迁移的消息。如果你感兴趣，可以在你的数据库命令行下输入： `\dt ` (PostgreSQL)， `SHOW TABLES; ` (MySQL)， `.schema` (SQLite)，或 `SELECT TABLE_NAME FROM USER_TABLES; ` (Oracle) 来列出 Django 所创建的表。

>  **极简主义者** 
> 就像我们上面所说的，一般情况下以上应用都默认包含在内，但不是每个人都需要它们。如果不需要某些或全部应用，在运行 `migrate` 命令前可从 INSTALLED_APPS 内随意注释或删除相应的行。 `migrate` 命令只会为 INSTALLED_APPS 内的应用创建表。

## 创建模型

现在我们要开始定义你的模型——本质上说，你的数据库布局和附加的元数据。

>  **哲理** 
> 一个模型是关于你的数据的唯一真实来源。 它包含您正在存储的数据的基本字段和行为。 Django遵循 [DRY原则](https://docs.djangoproject.com/en/1.11/misc/design-philosophies/#dry) 。 目标是在一个地方定义您的数据模型，并自动从中获取数据。
> 这包括迁移 - 与Ruby On Rails不同，例如，迁移完全源自您的模型文件，并且基本上只是Django可以通过更新数据库模式以匹配当前模型的历史。

在我们简单的投票应用中，我们创建两个模型： **Question** 与 **Choice。** 一个 **Question** 有一个问题和一个发布日期。 **Choice** 则有两个字段：选择的文本内容和对应的投票。 每个 **Choice** 与一个 **Question** 相关联。

这些概念由简单的Python类表示。 编辑 `polls/models.py` 文件，如下所示：

	# polls/models.py
	
	from django.db import models
	
	
	class Question(models.Model):
	 question_text = models.CharField(max_length=200)
	 pub_date = models.DateTimeField('date published')
	
	
	class Choice(models.Model):
	 question = models.ForeignKey(Question, on_delete=models.CASCADE)
	 choice_text = models.CharField(max_length=200)
	 votes = models.IntegerField(default=0)
	
	

代码很简单。每个模型都由一个继承自 [`django.db.models.Model`](https://docs.djangoproject.com/en/1.11/ref/models/instances/#django.db.models.Model) 的类来表示。每个模型都有一些类变量，每个变量都表示模型中的数据库字段。

每个字段由 [`Field`](https://docs.djangoproject.com/en/1.11/ref/models/fields/#django.db.models.Field) 类的实例表示，例如字符字段的CharField和数据时间的DateTimeField。这告诉Django每个字段拥有什么类型的数据。

每个Field实例的名称（例如question_text或pub_date）是以机器友好格式的字段名称。你会在Python代码中使用此值，数据库则将使用它作为列名。

您可以使用字段的可选的第一个位置参数来指定可读的名称。这用于Django的几个内省部分，它也可以作为文档。如果未提供此字段，Django将使用机器可读的名称。在这个例子中，我们只为Question.pub_date定义了一个可读的名字。对于此模型中的所有其他字段，该字段的机器可读名称将足以作为其可读的名称。

一些Field类有必要的参数。例如，CharField要求你给它一个max_length。这不仅在数据库模式中使用，而且用于验证，我们不就就可以看到。

字段也可以有各种可选参数；在这种情况下，我们将投票的默认值（default）设置为0。

最后注意，一个关系是用ForeignKey定义的。这告诉Django每个Chioce都与一个Question有关。 Django支持所有常见的数据库关系：多对一，多对多和一一对应。

## 激活模型

那一小段模型代码给了Django很多信息。 有了它，Django能够：

- 为此应用程序创建数据库模式（CREATE TABLE语句）。
- 创建一个Python数据库访问API来访问Question和Choice对象。

但首先我们需要告诉我们的项目，投票应用程序已经被安装了。

> **哲学**
> Django应用程序是“可插拔”：你可以在多个项目中使用应用程序，也可以分发应用程序，因为它们不必与给定的Django安装绑定。

要在我们的项目中包含该应用程序，我们需要在INSTALLED_APPS设置中添加对其配置类的引用。 PollConfig类在 `polls/apps.py` 文件中，因此它的点路径为“ `polls.apps.PollsConfig` ”。 编辑 `mysite/settings.py` 文件，并将该虚线路径添加到INSTALLED_APPS设置。 看起来像这样：

	# mysite/settings.py
	
	INSTALLED_APPS = [
	 'polls.apps.PollsConfig',
	 'django.contrib.admin',
	 'django.contrib.auth',
	 'django.contrib.contenttypes',
	 'django.contrib.sessions',
	 'django.contrib.messages',
	 'django.contrib.staticfiles',
	]

现在Django知道要包含polls应用，我们来运行另一个命令：

	$ python manage.py makemigrations polls

你会看见类似下面的一些输出：
```
Migrations for 'polls':
 polls/migrations/0001_initial.py:
 - Create model Choice
 - Create model Question
 - Add field question to choice
```
通过运行 `makemigrations` ，你告诉Django你已经对模型进行了一些更改（在这个例子中，你创建了新的模型），并且希望将这些更改存储为 _迁移_ 。

迁移是Django如何存储对模型（以及数据库模式）的更改 - 它们只是磁盘上的文件。 如果您喜欢，您可以阅读新模型的迁移信息；这是 `poll/migrations/0001_initial.py` 文件。 别担心，你不需要在Django每次生成文件时阅读它们，但是它们的设计是可编辑的，如果您想手动调整Django如何更改内容。

有一个命令可以自动为你运行迁移并管理数据库模式 - 这就是所谓迁移，我们稍后会介绍一下 - 但首先让我们看看迁移将运行什么SQL语句。 `sqlmigrate` 命令接受迁移名称并返回其SQL语句：

	$ python manage.py sqlmigrate polls 0001

你应该能看到类似下面的语句（我们已经重新格式化了这些语句以便更加可读）：

	BEGIN;
	--
	-- Create model Choice
	--
	CREATE TABLE "polls_choice" (
	 "id" serial NOT NULL PRIMARY KEY,
	 "choice_text" varchar(200) NOT NULL,
	 "votes" integer NOT NULL
	);
	--
	-- Create model Question
	--
	CREATE TABLE "polls_question" (
	 "id" serial NOT NULL PRIMARY KEY,
	 "question_text" varchar(200) NOT NULL,
	 "pub_date" timestamp with time zone NOT NULL
	);
	--
	-- Add field question to choice
	--
	ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
	ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
	CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
	ALTER TABLE "polls_choice"
	 ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
	 FOREIGN KEY ("question_id")
	 REFERENCES "polls_question" ("id")
	 DEFERRABLE INITIALLY DEFERRED;
	
	COMMIT;

请注意以下事项：

- 实际的输出将取决于您正在使用的数据库。以上示例为PostgreSQL生成。
- 表名称是通过组合应用程序的名称（polls）和模型的小写名称（question和choice）自动生成的。 （你可以重写此行为。）
- 主键（ID）将自动添加。 （你也可以重写这个。）
- 按照惯例，Django将“_id”附加到外键字段名称。 （是的，你也可以重写这个。）
- 外键关系由 `FOREIGN KEY` 约束显示。不要担心 **DEFERRABLE** 部分；这只是告诉PostgreSQL不执行外键直到事务结束。
- 它是针对您使用的数据库量身打造的，因此数据库特定的字段类型（如 `auto_increment` （MySQL））， `serial` （PostgreSQL）或 `integer primary key autoincrement` 整数主键自动增量（SQLite））会自动为您处理。引用字段名称也是如此，例如使用双引号或单引号。
-  `sqlmigrate` 命令实际上不会在数据库上运行迁移 - 它只是打印到屏幕上，以便您可以看到Django认为需要什么SQL语句。检查Django要执行的操作或者是否有需要对SQL脚本进行更改的数据库管理员很有用。

如果你有兴趣，还可以运行 `python manage.py check` ;这将检查项目中的任何问题，而不进行迁移或改变数据库。

现在，再次运行迁移以在数据库中创建这些模型表：

	$ python manage.py migrate
	Operations to perform:
	 Apply all migrations: admin, auth, contenttypes, polls, sessions
	Running migrations:
	 Rendering model states... DONE
	 Applying polls.0001_initial... OK

 `migrate` 命令接收所有未应用的迁移（Django使用数据库中称为 `django_migrations` 的特殊表来跟踪哪些迁移已经被应用），并根据数据库运行它们 - 实质上，将你对模型所做的更改与模式进行同步在数据库中。

迁移非常强大，您可以随时更改模型，而无需删除数据库或表，或创建新的模型 - 它专门用于实时升级数据库，而不会丢失数据。我们将在本教程的后续部分中更深入地介绍它们，但是现在，请记住进行模型更改的三步指南：

- 更改你的模型（在models.py中）。
- 运行 `python manage.py makemigrations` 以创建这些更改的迁移
- 运行 `python manage.py migrate` 将这些更改应用于数据库。

有单独的命令来制作和应用迁移的原因是因为您将把迁移提交到版本控制系统并随着应用程序移动；它们不仅可以使您的开发更容易，而且还可以被其他开发人员和在生产中使用。

请阅读 [django-admin文档](https://docs.djangoproject.com/en/1.10/ref/django-admin/) ，了解有关 `manage.py` 的作用的完整信息。

## 体验API

现在，让我们进入交互式的Python shell，并使用Django提供的免费API。要调用Python shell，请使用以下命令：

	$ python manage.py shell

我们使用这个命令而不是简单地输入“python”，是因为 `manage.py` 设置了 `DJANGO_SETTINGS_MODULE` 环境变量，这给Django的 `mysite / settings.py` 文件提供了Python导入路径。

> **绕过manage.py**
> 如果你不想使用 `manage.py` ，没问题。只需将 `DJANGO_SETTINGS_MODULE` 环境变量设置为 `mysite.settings` ，启动一个简单的Python shell，并设置Django：
>```
>>>>import django
>>>>django.setup（）`
>```
> 如果这引发了 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) ，那么您可能会使用与本教程版本不符的Django版本。您将需要切换到较旧的教程或较新的Django版本。
> 你必须从 `manage.py` 的同一个目录下运行 `python` ，或者确保该目录在Python路径上，以便 `import mysite` 语句能够工作。
> 有关所有这些的更多信息，请参阅 [django-admin文档](https://docs.djangoproject.com/en/1.10/ref/django-admin/) 。

一旦你进入shell，就可以探索数据库API：

	>>> from polls.models import Question, Choice # Import the model classes we just wrote.
	
	# No questions are in the system yet.
	>>> Question.objects.all()
	<QuerySet []>
	
	# Create a new Question.
	# Support for time zones is enabled in the default settings file, so
	# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
	# instead of datetime.datetime.now() and it will do the right thing.
	>>> from django.utils import timezone
	>>> q = Question(question_text="What's new?", pub_date=timezone.now())
	
	# Save the object into the database. You have to call save() explicitly.
	>>> q.save()
	
	# Now it has an ID. Note that this might say "1L" instead of "1", depending
	# on which database you're using. That's no biggie; it just means your
	# database backend prefers to return integers as Python long integer
	# objects.
	>>> q.id
	1
	
	# Access model field values via Python attributes.
	>>> q.question_text
	"What's new?"
	>>> q.pub_date
	datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)
	
	# Change values by changing the attributes, then calling save().
	>>> q.question_text = "What's up?"
	>>> q.save()
	
	# objects.all() displays all the questions in the database.
	>>> Question.objects.all()
	<QuerySet [<Question: Question object>]>

等一下。 `<Question: Question object>` 完全是这个对象的无意义表示。 我们通过编辑 `Question` 模型（在 `polls / models.py` 文件中）并将 `__str __()` 方法添加到 `Question` 和 `Choice` 来修正这个问题：
```
# polls/models.py

from django.db import models
from django.utils.encoding import python_2_unicode_compatible

python_2_unicode_compatible # only if you need to support Python 2
class Question(models.Model):
	# ...
	def __str__(self):
		return self.question_text

@python_2_unicode_compatible # only if you need to support Python 2
class Choice(models.Model):
	# ...
	def __str__(self):
		return self.choice_text
```
将 `__str __()` 方法添加到模型中是非常重要的，不仅仅是为了在交互式提示时方便处理，同时也因为这个表示被用在Django自动生成的管理站点中。

注意这些是普通的Python方法。 我们添加一个自定义的方法，只是为了演示：

	# polls/models.py
	import datetime
	
	from django.db import models
	from django.utils import timezone
	
	
	class Question(models.Model):
		# ...
		def was_published_recently(self):
			return self.pub_date >= timezone.now() - datetime.timedelta(days=1)

请注意，添加 `import datetime` 和 `from django.utils import timezone` 可以分别引用 `django.utils.timezone` 中的Python的标准 `datetime` 模块和Django的时区相关实用程序。 如果您不熟悉Python中的时区处理，您可以在 [时区支持文档](https://docs.djangoproject.com/en/1.11/topics/i18n/timezones/) 中了解更多信息。

保存这些更改并通过再次运行 `python manage.py shell` 启动一个新的Python交互式shell：

	>>> from polls.models import Question, Choice
	
	# Make sure our __str__() addition worked.
	>>> Question.objects.all()
	<QuerySet [<Question: What's up?>]>
	
	# Django provides a rich database lookup API that's entirely driven by
	# keyword arguments.
	>>> Question.objects.filter(id=1)
	<QuerySet [<Question: What's up?>]>
	>>> Question.objects.filter(question_text__startswith='What')
	<QuerySet [<Question: What's up?>]>
	
	# Get the question that was published this year.
	>>> from django.utils import timezone
	>>> current_year = timezone.now().year
	>>> Question.objects.get(pub_date__year=current_year)
	<Question: What's up?>
	
	# Request an ID that doesn't exist, this will raise an exception.
	>>> Question.objects.get(id=2)
	Traceback (most recent call last):
	 ...
	DoesNotExist: Question matching query does not exist.
	
	# Lookup by a primary key is the most common case, so Django provides a
	# shortcut for primary-key exact lookups.
	# The following is identical to Question.objects.get(id=1).
	>>> Question.objects.get(pk=1)
	<Question: What's up?>
	
	# Make sure our custom method worked.
	>>> q = Question.objects.get(pk=1)
	>>> q.was_published_recently()
	True
	
	# Give the Question a couple of Choices. The create call constructs a new
	# Choice object, does the INSERT statement, adds the choice to the set
	# of available choices and returns the new Choice object. Django creates
	# a set to hold the "other side" of a ForeignKey relation
	# (e.g. a question's choice) which can be accessed via the API.
	>>> q = Question.objects.get(pk=1)
	
	# Display any choices from the related object set -- none so far.
	>>> q.choice_set.all()
	<QuerySet []>
	
	# Create three choices.
	>>> q.choice_set.create(choice_text='Not much', votes=0)
	<Choice: Not much>
	>>> q.choice_set.create(choice_text='The sky', votes=0)
	<Choice: The sky>
	>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)
	
	# Choice objects have API access to their related Question objects.
	>>> c.question
	<Question: What's up?>
	
	# And vice versa: Question objects get access to Choice objects.
	>>> q.choice_set.all()
	<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
	>>> q.choice_set.count()
	3
	
	# The API automatically follows relationships as far as you need.
	# Use double underscores to separate relationships.
	# This works as many levels deep as you want; there's no limit.
	# Find all Choices for any question whose pub_date is in this year
	# (reusing the 'current_year' variable we created above).
	>>> Choice.objects.filter(question__pub_date__year=current_year)
	<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
	
	# Let's delete one of the choices. Use delete() for that.
	>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
	>>> c.delete()

有关模型关系的更多信息，请参阅 [访问相关对象](https://docs.djangoproject.com/en/1.11/ref/models/relations/) 。有关如何使用双下划线通过API执行字段查找的更多信息，请参阅 [字段查找](https://docs.djangoproject.com/en/1.11/topics/db/queries/#field-lookups-intro) 。有关数据库API的完整详细信息，请参阅我们的 [数据库API参考](https://docs.djangoproject.com/en/1.11/topics/db/queries/) 。

## 介绍Django Admin

> **哲学**
> 为您的员工或客户生成管理网站来添加，更改和删除内容是繁琐的工作，而且不需要太多的创造力。为此，Django完全自动创建模型的管理界面。
> Django是在新闻编辑室环境中编写的，“内容发布者”和“公共”网站之间的界限非常明确。网站管理人员使用该系统添加新闻故事，事件，体育比分等，内容则显示在公共站点上。 Django解决了为站点管理员创建统一界面来编辑内容的问题。
> 管理员不打算由网站访问者使用。这是为网站管理人员准备的。

### 创建管理员用户

首先，我们需要创建一个可以登录管理站点的用户。运行以下命令：

	$ python manage.py createsuperuser

输入你需要的用户名然后按下回车。

	Username: admin

接下来你会被提示输入你需要的邮箱地址：

	Email address: admin@example.com

最后一步是输入密码，你会被要求输入两次，第二次是为了确认第一次的输入无误。

	Password: **********
	Password (again): *********
	Superuser created successfully.

### 启动开发服务器

默认情况下，Django的管理站点是激活的。 让我们启动开发服务器并探索它。

如果服务器在运行，它就像这样：

	$ python manage.py runserver

现在，打开浏览器，然后转到本地的“ admin/” - 例如 `http://127.0.0.1:8000/admin/` 。 你应该会看到管理员的登录界面：

![](https://docs.djangoproject.com/en/1.11/_images/admin01.png)

由于默认情况下 [翻译](https://docs.djangoproject.com/en/1.11/topics/i18n/translation/) 是打开的，因此如果Django有该语言的翻译，登录界面可能会以您自己的语言显示，具体取决于浏览器的设置。

### 进入管理站点

现在，尝试使用之前创建的超级管理员账号登入。你应该能看到Django管理站点的首页：

![](https://docs.djangoproject.com/en/1.11/_images/admin02.png)

您应该看到几种类型的可编辑内容：组和用户。 它们由Django提供的认证框架 `django.contrib.auth` 提供。

## 使投票应用程序在管理站点中可修改

但是我们的投票应用程序在哪里？ 它不显示在管理员索引页面上。

只需要做一件事：我们需要告诉管理员 `Question` 对象有一个管理界面。 要做到这一点，打开 `poll/admin.py` 文件，并将其编辑如下所示：

	# polls/admin.py
	from django.contrib import admin
	
	from .models import Question
	
	admin.site.register(Question)

## 探索免费的管理功能

现在我们已经把 `Question` 注册了，Django知道它应该被展示在管理站点的首页上：

![](https://docs.djangoproject.com/en/1.11/_images/admin03t.png)

点击”Question“，现在你在Question的“修改列表”页面上，这个页面展示数据库里所有的问题，并且你可以选择一个来修改。这里时我们之前创建的问题“What’s up?”：

![](https://docs.djangoproject.com/en/1.11/_images/admin04t.png)

点击“What’s up?”问题来编辑它：

![](https://docs.djangoproject.com/en/1.11/_images/admin05t.png)

注意事项：

- 该表单自动从Question模型生成。
- 不同的模型字段类型（DateTimeField，CharField）对应于适当的HTML输入控件。每种类型的字段知道如何在Django管理站点中显示自己。
- 每个 `DateTimeField` 都有方便的的JavaScript快捷按钮。日期有一个“Today”快捷按钮和日历弹出窗口，时间则有一个“Now”快捷按钮和一个方便的弹出窗口列出常用的时间。

页面底部提供了几个选项：

- 保存 - 保存更改并返回到此类型对象的更改列表页面。
- 保存并继续编辑 - 保存更改并重新加载此对象的管理页面。
- 保存并添加另一个 - 保存更改并加载此类型对象的新的空白表单。
- 删除 - 显示删除确认页面。

如果“发布日期”的值与 [教程1](https://docs.djangoproject.com/en/1.11/intro/tutorial01/) 中创建问题的时间不匹配，则可能意味着您忘记为TIME_ZONE设置设置了正确的值。修改它，然后重新加载页面，并检查是否显示正确的值。

点击“Today”和“Now”快捷按钮来更改“发布日期”。然后点击“保存并继续编辑”，然后点击右上角的“历史记录”。您将看到一个页面，其中列出了通过Django管理员对此对象所做的所有更改，以及进行更改的人员的时间戳和用户名：

![](https://docs.djangoproject.com/en/1.11/_images/admin06t.png)

当您对模型API感到满意并熟悉管理员站点时，请阅读本教程的 [第3部分](https://docs.djangoproject.com/en/1.11/intro/tutorial03/) ，了解如何向我们的投票应用添加更多视图。