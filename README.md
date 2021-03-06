django-inheritance
==================

An implementation of prototypal inheritance for Django model instances.

Motivation
----------

Prototypal inheritance can be a very handy concept for some subject areas. For example, you have got a `Company` model with a hierarchy defined (any company can have several subsidiaries). It is often the case that subsidiaries inherit lots of field values from parents, for example -- city or juridical type. `django-inheritance` allows to mark model fields inheritable. Imagine that `Company` model has `city` field which is inheritable. Then:

```python
>>> parent = Company.objects.create(city='NY')
>>> child  = Company.objects.create(parent=parent)
>>> child.city
'NY'
```

As `child.city` is empty, it inherits value from parent object.

Usage
-----

* Install prerequisites:

```bash
pip install django-mptt
pip install django-dirtyfields
```

* Copy `inheritance` folder somewhere at your Python path (for example, straight to your project folder).
* Set up your `INSTALLED_APPS`:

```python
INSTALLED_APPS = (
    ...
    'mptt',
    'inheritance',
)
```

* Set up the target model:

```python
from django.db import models
from inheritance.models import Inheritable
from mptt.fields import TreeForeignKey

class MyModel(Inheritable):
    # Required for mptt. Denotes the model hierarchy.
    parent = TreeForeignKey('self')

    # Your own fields... For example:
    name = models.CharField(max_length=100)
    desc = models.TextField()

    # List all the inheritable fields
    inherit_fields = ('description', )
```

* Optionally, if you use Django admin, you can set up a ModelAdmin for your model:

```python
from django.contrib.admin import site
from inheritance.admin import InheritanceAdmin

class MyAdmin(InheritanceAdmin):
    fieldsets = (
        (None, {'fields': ('name', 'desc')}),
    )
```

You can see a working Django example project at `sampleproject` directory. Just run there:

```python
./manage.py runserver
```

Development
-----------

For tests, you need to install webtest:

```bash
pip install django_webtest
```

and run

```bash
./manage.py test sampleapp
```

Status
------

This app is being actively developed and used in a production system under Python 2.6/2.7 and Django 1.6. But is it yet in alpha stage because many features are not implemented/tested. Known problems:

* Not tested with any field types besides `CharField`, `TextField`, and `ForeignKey`.
* You cannot use an empty value without inheriting parent value.
* In model admin class, you must specify the `fieldsets` property for the app to work.
* There is no support for custom model forms (besides those used in admin section).
* No support for inherited attached inline models. In other words, if parent company has an Asset object attached, cannot be inherited by a child company.

Acknowledgements
----------------

This app development is funded by Vboost Inc., http://vboost.com. Released to Open Source with permission. You can use, modify, and redistribute it under the terms of MIT software license.