原文：[How to Extend Django User Model](http://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html)


--- 
![如何扩展Django User模型](http://simpleisbetterthancomplex.com/media/2016-07-22-how-to-extend-django-user-model/featured-post-image.jpg)

Django内置的验证系统棒棒哒。大部分，我们可以直接使用，省去大量的开发和测试工作。它适用于大多数的用户场景，并且非常安全。但是，有时，我们需要进行一些微调，以适配我们自己的Web应用。

通常，我们想要存储关于我们的User的更多的信息。如果你的Web应用面向公众，那么可能你会想要存储一个简短的个人介绍、用户的位置，诸如此类信息。

在本教程中，我将介绍你可以用来简单扩展默认的Django User模型的策略，这样，你就不需要一切从头开始。

* * *

#### 扩展现有的User模型的方法

一般来讲，有四种不同的方法来扩展现有的User模型。下面是为什么以及何时使用它们。

##### **选项1：** 使用一个Proxy模型

**Proxy模型是什么？**  

它是一个模型继承，无需在数据库中创建新的表。它用于改变现有模型的行为（例如，默认的排序，新增一些方法，等等），而不会影响现有的数据库模式。

**什么时候我应该使用Proxy模型？**  

当你不需要存储额外的信息到数据库，而只是添加额外的方法或更改模型的查询Manager的时候，你应该使用一个Proxy模型来扩展现有的User模型。

这就是我所需要的！[带我去说明部分。](#使用一个Proxy模型来扩展User模型)

##### **选项2：** 使用与User模型的一对一链接（Profile）

**什么是一对一链接？**  

这是一个普通的Django模型，它有自己的数据库表，并通过`OneToOneField`来存储与现有User模型的一对一关系。

**何时使用一对一链接？**  
当你需要存储关于现有User模型的额外信息（该信息与鉴权过程无关）时，你应该使用一对一链接。我们通常称之为User Profile

这就是我所需要的！[带我去说明部分。](#使用一个一对一链接来扩展User模型)

##### **选项3：** 创建一个扩展AbstractBaseUser的自定义的User模型

**扩展AbstractBaseUser的自定义的User模型是什么？** 

这是一个继承于`AbstractBaseUser`的全新的User模型。它需要特别谨慎，以及通过`settings.py`更新一些引用。理想情况下，应该在项目的开始时完成，因为它会极大影响到数据库模式。实现的时候，要额外谨慎。

**什么时候应该使用扩展AbstractBaseUser的自定义的User模型？**  

当你的应用有一些与鉴权过程有关的特殊需求的时候，你应该使用一个自定义的User模型。例如，在某些情况下，使用电子邮箱地址，而不是用户名作为识别标志，会更有意义些。

这就是我所需要的！[带我去说明部分。](#使用一个扩展AbstractBaseUser的自定义的模型来扩展User模型)

##### **选项4：** 创建一个扩展AbstractUser的自定义的User模型

**扩展AbstractUser的自定义的User模型是什么？**  

它是一个继承于`AbstractUser`的新的User模型。它需要特别谨慎，以及通过`settings.py`更新一些引用。理想情况下，应该在项目的开始时完成，因为它会极大影响到数据库模式。实现的时候，要额外谨慎。

**什么时候应该使用扩展AbstractUser的自定义的User模型？**  

当你非常满意Django处理鉴权过程的方式，并且你不会在其上做任何改变的时候，你应该使用它。然而，你想要直接在User模型上添加一些额外的信息，而不必创建一个额外的类（像**选项2**中那样）。

这就是我所需要的！[带我去说明部分。](#使用一个扩展AbstractUser的自定义的模型来扩展User模型)

* * *

#### 使用一个Proxy模型来扩展User模型

这是扩展现有User模型最不侵入的方式。使用这个策略，你不会有任何缺点。但它在许多方面非常有限。

下面是如何做到这点：

```python    
    from django.contrib.auth.models import User
    from .managers import PersonManager
    
    class Person(User):
        objects = PersonManager()
    
        class Meta:
            proxy = True
            ordering = ('first_name', )
    
        def do_something(self):
            ...
```

上面的例子中，我们定义了一个名为`Person`的Proxy模型。我们通过在Meta类中添加`proxy = True`属性，来告诉Django，这是一个Proxy模型。

在这个例子中，我重新定义了默认的排序，将一个自定义的`Manager`赋给该模型，还定义了一个新方法`do_something`。

值得注意的是，`User.objects.all()`和`Person.objects.all()`将会查询相同的数据库表。唯一的不同是，我们为Proxy模型定义的行为。

如果这就是你所想要的，拿去。把事情简单化。

* * *

#### 使用一个一对一链接来扩展User模型

这极有可能就是你想要的。就个人来讲，这是大部分我使用的方法。我们将创建一个新的Django模型来存储与User模型有关的额外信息。

请记住，使用这个策略会导致额外的查询或者结合到检索相关数据。基本上，每次你访问一个相关数据，Django将会触发一个额外的查询。但在大多数情况下，这是可以避免的。稍后，我将提到如何避免。

我通常将这个Django模型命名为`Profile`：

    
```python    
    from django.db import models
    from django.contrib.auth.models import User
    
    class Profile(models.Model):
        user = models.OneToOneField(User, on_delete=models.CASCADE)
        bio = models.TextField(max_length=500, blank=True)
        location = models.CharField(max_length=30, blank=True)
        birth_date = models.DateField(null=True, blank=True)
```

现在，这是出现不可思议的地方：现在，我们会定义**信号**，这样，当我们创建/更新User实例的时候，我们的`Profile`模型将会自动的创建/更新。

    
```python    
    from django.db import models
    from django.contrib.auth.models import User
    from django.db.models.signals import post_save
    from django.dispatch import receiver
    
    class Profile(models.Model):
        user = models.OneToOneField(User, on_delete=models.CASCADE)
        bio = models.TextField(max_length=500, blank=True)
        location = models.CharField(max_length=30, blank=True)
        birth_date = models.DateField(null=True, blank=True)
    
    @receiver(post_save, sender=User)
    def create_user_profile(sender, instance, created, **kwargs):
        if created:
            Profile.objects.create(user=instance)
    
    @receiver(post_save, sender=User)
    def save_user_profile(sender, instance, **kwargs):
        instance.profile.save()
```
基本上，我们挂接`create_user_profile`和`save_user_profile`方法到User模型，无论何时**保存**事件发生。这种信号被称作`post_save`。

好东西。_现在，告诉我如何使用它_。

小菜一碟。看看Django模板中的这个例子：

    
```html    
    <h2>{{ user.get_full_name }}</h2>
    <ul>
      <li>Username: {{ user.username }}</li>
      <li>Location: {{ user.profile.location }}</li>
      <li>Birth Date: {{ user.profile.birth_date }}</li>
    </ul>
```
_视图方法里面怎样？_

    
```python    
    def update_profile(request, user_id):
        user = User.objects.get(pk=user_id)
        user.profile.bio = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit...'
        user.save()
```
一般来说，你将永远不会调用Profile的save方法。一切都是通过User模型完成的。

_如果我使用Django表单呢？_

你知道你可以同时处理多个表单吗？看看这个片段：

**forms.py**
    
```python    
    class UserForm(forms.ModelForm):
        class Meta:
            model = User
            fields = ('first_name', 'last_name', 'email')
    
    class ProfileForm(forms.ModelForm):
        class Meta:
            model = Profile
            fields = ('url', 'location', 'company')
```
**views.py**
    
```python    
    @login_required
    @transaction.atomic
    def update_profile(request):
        if request.method == 'POST':
            user_form = UserForm(request.POST, instance=request.user)
            profile_form = ProfileForm(request.POST, instance=request.user.profile)
            if user_form.is_valid() and profile_form.is_valid():
                user_form.save()
                profile_form.save()
                messages.success(request, _('Your profile was successfully updated!'))
                return redirect('settings:profile')
            else:
                messages.error(request, _('Please correct the error below.'))
        else:
            user_form = UserForm(instance=request.user)
            profile_form = ProfileForm(instance=request.user.profile)
        return render(request, 'profiles/profile.html', {
            'user_form': user_form,
            'profile_form': profile_form
        })
```
**profile.html**
    
```html    
    <form method="post">
      {% csrf_token %}
      {{ user_form.as_p }}
      {{ profile_form.as_p }}
      <button type="submit">Save changes</button>
    </form>
```
_以及你在说的额外的数据库查询呢？_

噢，是的。我在另一篇名为“优化数据库查询”的文章中处理了这个问题。你可以[点击这里](http://simpleisbetterthancomplex.com/tips/2016/05/16/django-tip-3-optimize-database-queries.html)来看一看。

但，长话短说：Django的关系是惰性的。意味着，Django只有在你访问其中一个相关属性的时候才会进行数据库查询。有时候，这引发了一些期望外的效果，例如触发数百上千的查询。这个问题可以使用`select_related`方法来减缓。

事先知道你将需要访问的相关数据，你可以在一个单一的数据库查询中预取：
    
```python    
    users = User.objects.all().select_related('profile')
```
* * *

#### 使用一个扩展AbstractBaseUser的自定义的模型来扩展User模型

令人心惊的一个选择。好吧，老实说，我都是不惜一切代价来避免使用它的。但有时候，你无法避免。并且它完全可行。几乎没有一件事（像它一样）既是天使又是魔鬼。在大多数情况下，或多或少有个合适的解决方案。如果这在你当前情况下是最合适的解决方法，那就看下去吧。

我必须一次做完。老实说，我不知道这是否是做到这点更清晰的方式，但是，不管那么多了：

我需要将电子邮件地址作为身份验证令牌，而在此场景下，`username`对我完全没用。另外，也不需要`is_staff`标志，因为我没有使用Django Admin。

下面是我如何定义我自己的用户模型的：
    
```python    
    from __future__ import unicode_literals
    
    from django.db import models
    from django.contrib.auth.models import PermissionsMixin
    from django.contrib.auth.base_user import AbstractBaseUser
    from django.utils.translation import ugettext_lazy as _
    
    from .managers import UserManager
    
    
    class User(AbstractBaseUser, PermissionsMixin):
        email = models.EmailField(_('email address'), unique=True)
        first_name = models.CharField(_('first name'), max_length=30, blank=True)
        last_name = models.CharField(_('last name'), max_length=30, blank=True)
        date_joined = models.DateTimeField(_('date joined'), auto_now_add=True)
        is_active = models.BooleanField(_('active'), default=True)
        avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
    
        objects = UserManager()
    
        USERNAME_FIELD = 'email'
        REQUIRED_FIELDS = []
    
        class Meta:
            verbose_name = _('user')
            verbose_name_plural = _('users')
    
        def get_full_name(self):
            '''
            Returns the first_name plus the last_name, with a space in between.
            '''
            full_name = '%s %s' % (self.first_name, self.last_name)
            return full_name.strip()
    
        def get_short_name(self):
            '''
            Returns the short name for the user.
            '''
            return self.first_name
    
        def email_user(self, subject, message, from_email=None, **kwargs):
            '''
            Sends an email to this User.
            '''
            send_mail(subject, message, from_email, [self.email], **kwargs)
```

我想让它尽可能接近现有的User模型。由于我们是从`AbstractBaseUser`继承的，因此必须遵循一些规则：

  * **USERNAME_FIELD**: 一个描述User模型名字字段的字符串，作为唯一标识。该字段必须唯一 (即，在其定义中，必须设置`unique=True`);
  * **REQUIRED_FIELDS**: 一个字段名列表，用于当通过`createsuperuser`管理命令创建一个用户时的提示；
  * **is_active**: 一个布尔值属性，表示用户是否被认为是“活跃的(active)”;
  * **get_full_name():** 用户的一个更长的正式标识符。一个常见的理解是用户的全名，但它可以是标识该用户的任何字符串。
  * **get_short_name():** 用户的一个简短的非正式标识符。一个常见的理解是用户的名。

好吧，让我们继续。我还必须定义自己的`UserManager`。这是因为现有的manager定义了`create_user`和`create_superuser`方法。

所以，下面是我的`UserManager`的样子：
    
```python    
    from django.contrib.auth.base_user import BaseUserManager
    
    class UserManager(BaseUserManager):
        use_in_migrations = True
    
        def _create_user(self, email, password, **extra_fields):
            """
            Creates and saves a User with the given email and password.
            """
            if not email:
                raise ValueError('The given email must be set')
            email = self.normalize_email(email)
            user = self.model(email=email, **extra_fields)
            user.set_password(password)
            user.save(using=self._db)
            return user
    
        def create_user(self, email, password=None, **extra_fields):
            extra_fields.setdefault('is_superuser', False)
            return self._create_user(email, password, **extra_fields)
    
        def create_superuser(self, email, password, **extra_fields):
            extra_fields.setdefault('is_superuser', True)
    
            if extra_fields.get('is_superuser') is not True:
                raise ValueError('Superuser must have is_superuser=True.')
    
            return self._create_user(email, password, **extra_fields)
```

基本上，我已经完成了对现有`UserManager`的清理，移除`username`和`is_staff`属性。

现在是最后一步。我们必须更新我们的settings.py。更具体的是`AUTH_USER_MODEL`属性。
    
```python    
    AUTH_USER_MODEL = 'core.User'
```
这样，我们告诉Django使用我们自定义的模型，而不是默认的那个。在上面的例子中，我在一个名为`core`的app中创建了这个自定义模型。

_我应该如何引用这个模型呢？_

好，有两种方式。想想一个名为`Course`的模型：
    
```python    
    from django.db import models
    from testapp.core.models import User
    
    class Course(models.Model):
        slug = models.SlugField(max_length=100)
        name = models.CharField(max_length=100)
        tutor = models.ForeignKey(User, on_delete=models.CASCADE)
```
这是完全没问题。但是，如果你正在创建一个可重复使用的app，并且你想将其公开，那么我们强烈建议你使用以下策略：
    
```python    
    from django.db import models
    from django.conf import settings
    
    class Course(models.Model):
        slug = models.SlugField(max_length=100)
        name = models.CharField(max_length=100)
        tutor = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```
* * *

#### 使用一个扩展AbstractUser的自定义的模型来扩展User模型

这是非常简单明了，因为`django.contrib.auth.models.AbstractUser`类，作为一个抽象模型，提供了默认的User的完整实现。
    
```python    
    from django.db import models
    from django.contrib.auth.models import AbstractUser
    
    class User(AbstractUser):
        bio = models.TextField(max_length=500, blank=True)
        location = models.CharField(max_length=30, blank=True)
        birth_date = models.DateField(null=True, blank=True)
```
然后，我们必须更新我们的settings.py，定义`AUTH_USER_MODEL`属性。
    
```python    
    AUTH_USER_MODEL = 'core.User'
```
以与前面的方法类似的方式，理想情况下，这应该在一个项目的开头完成，并且应该小心谨慎。它会改变整个数据库模式。此外，喜欢创建外键的用户模型导入设置from django.conf import settings，并指settings.AUTH_USER_MODEL直接引用自定义用户模型来代替。

此外，创建到User模型的外键，导入配置`from django.conf import settings`，以及引用`settings.AUTH_USER_MODEL`而不是直接引用自定义的User模型，这样会更好。

* * *

#### 总结

好的！我们通过四种不同的方式来扩展现有的用户模型。我试着尽可能多的告诉你细节。正如我以前说过的，没有_最好的解决方案_。这将真正取决于你需要达到的目标。保持简单，并且明智地选择。

  * **Proxy模型：** 你对Django User提供的一切都感到满意，并且不想要存储额外的信息。
  * **User Profile:** 你对Django处理鉴权的过程感到满意，并且需要添加一些鉴权无关的属性到User。
  * **继承自AbstractBaseUser的自定义的User模型：** Django处理鉴权的方式并不适合你的项目。
  * **继承自AbstractUser自定义的User模型：** Django处理鉴权的方式非常适合你对项目，但你仍想要添加额外的属性，而不想要创建一个单独的模型。

不要犹豫，问我问题吧，或者告诉我你对这篇文章的看法！

你也可以[加入我的邮件列表](http://eepurl.com/b0gR51)。每周，我直接发送专属提示到你的邮箱！ :-)
