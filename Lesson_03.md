# Lesson 3: Models

## Start
Inside the ``foundation`` application start by deleting the ``models.py`` file and run the following.

```bash
rm foundation/models.py
mkdir foundation/models
touch foundation/models/__init__.py
touch foundation/models/tag.py
touch foundation/models/web_archive.py
```

## Tag
Inside our ``tag.py`` file, please populate the following:

```python
"""
foundation/models/tag.py
"""
from django.db import models
from django.utils.translation import ugettext_lazy as _


class Tag(models.Model):

    '''
    METADATA
    '''

    class Meta:
        app_label = 'foundation'
        db_table = 'burrow_tags'
        verbose_name = _('Tag')
        verbose_name_plural = _('Tags')
        default_permissions = ()
        permissions = (
        #     ("can_get_opening_hours_specifications", "Can get opening hours specifications"),
        #     ("can_get_opening_hours_specification", "Can get opening hours specifications"),
        #     ("can_post_opening_hours_specification", "Can create opening hours specifications"),
        #     ("can_put_opening_hours_specification", "Can update opening hours specifications"),
        #     ("can_delete_opening_hours_specification", "Can delete opening hours specifications"),
        )

    '''
    MODEL FIELDS
    '''

    text = models.CharField(max_length=255, unique=True,)

    '''
    MODEL FUNCTIONS
    '''

    def __str__(self):
        """
        Function called when the object gets cast into a string.
        """
        return self.text
```

## WebArchive
Inside our ``web_archive.py`` file, please populate the following:

```python
"""
foundation/models/web_archive.py
"""
from django.db import models
from django.utils.translation import ugettext_lazy as _
from django.contrib.auth.models import User


class WebArchiveArchiveManager(models.Manager):
    def delete_all(self):
        items = WebArchiveArchive.objects.all()
        for item in items.all():
            item.delete()


class WebArchive(models.Model):

    '''
    METADATA
    '''

    class Meta:
        app_label = 'foundation'
        db_table = 'burrow_web_archives'
        verbose_name = _('WebArchive')
        verbose_name_plural = _('WebArchive')
        default_permissions = ()
        permissions = (
        #     ("can_get_opening_hours_specifications", "Can get opening hours specifications"),
        #     ("can_get_opening_hours_specification", "Can get opening hours specifications"),
        #     ("can_post_opening_hours_specification", "Can create opening hours specifications"),
        #     ("can_put_opening_hours_specification", "Can update opening hours specifications"),
        #     ("can_delete_opening_hours_specification", "Can delete opening hours specifications"),
        )

    '''
    CONSTANTS
    '''

    class STATE:
        ERROR = 1
        WAITING = 2
        PROCESSING = 3
        PRIVATE = 4
        PUBLIC = 5
        ARCHIVED = 6

    '''
    CHOICES
    '''

    STATE_CHOICES = (
        (STATE.ERROR, _('Error')),
        (STATE.WAITING, _('Waiting')),
        (STATE.PROCESSING, _('Processing')),
        (STATE.PRIVATE, _('Privately Available')),
        (STATE.PUBLIC, _('Publically Available')),
        (STATE.ARCHIVED, _('Archived')),
    )

    '''
    OBJECT MANAGERS
    '''

    objects = WebArchiveArchiveManager()

    '''
    MODEL FIELDS
    '''

    original_url = models.URLField(
        _("Original URL"),
        help_text=_('The original URL of this web-archive.'),
        null=False,
        blank=False
    )
    state = models.PositiveSmallIntegerField(
        _("State"),
        help_text=_('The state that this object is in.'),
        default=STATE.WAITING,
        blank=True,
        choices=STATE_CHOICES,
    )
    user = models.ForeignKey(
        User,
        on_delete=models.CASCADE,
        help_text=_('The user whom created this web-archive.'),
        null=False,
        blank=False,
        related_name="web_archives"
    )
    liked = models.ManyToManyField(
        to=User,
        blank=True,
        related_name="liked_web_archives"
    )
    tags = models.ManyToManyField(
        to="Tag",
        blank=True,
        related_name="web_archives"
    )
    created = models.DateTimeField(auto_now_add=True)
    last_modified = models.DateTimeField(auto_now=True)

    '''
    MODEL FUNCTIONS
    '''

    def __str__(self):
        """
        Function called when the object gets cast into a string.
        """
        return str(self.original_url)

    def get_state_label(self):
        """
        Function returns the string text of the selected choice label.
        """
        choice = dict(STATE_CHOICES).get(self.state)
        return str(choice)
```

## Finally

Inside our ``__init__.py`` file, please populate the following:

```python
"""
foundation/__init__.py
"""

from foundation.models.tag import Tag
from foundation.models.web_archive import WebArchive
```

Build our database.

```bash
python manage.py makemigrations
python manage.py migrate
```

Inside our ``admin.py``, please populate the following:

```python
from django.contrib import admin

from .models import *
admin.site.register(WebArchive)
```
