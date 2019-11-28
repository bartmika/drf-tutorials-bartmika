# How to Upload a Base64 File using Django REST Framework

The purpose of this lesson is to show you how to upload files and submit data.

The assumption of this lesson is you need to have completed [Lesson 01](https://github.com/bci-innovation-labs/drf-tutorials-bartmika/blob/master/Lesson_01.md) before proceeding.

## Step 1: Model

Inside your ``foundation/models`` folder, please create a ``url_request_file_upload.py`` file with the following contents:

```python

from django.db import models
from django.utils.translation import ugettext_lazy as _


class URLRequestFileUploadManager(models.Manager):
    def delete_all(self):
        items = URLRequestFileUpload.objects.all()
        for item in items.all():
            item.delete()

class URLRequestFileUpload(models.Model):
    """
    Upload image class which is publically accessible to anonymous users
    and authenticated users.
    """
    class Meta:
        app_label = 'foundation'
        db_table = 'burrow_url_request_file_uploads'
        verbose_name = _('URL Request File Upload')
        verbose_name_plural = _('URL Request File Uploads')
        default_permissions = ()
        permissions = ()

    objects = URLRequestFileUploadManager()

    #
    #  FIELDS
    #

    data_file = models.FileField(
        upload_to = 'uploads/%Y/%m/%d/',
        help_text=_('The upload binary file.'),
    )
    title = models.CharField(
        _("Title"),
        max_length=63,
        help_text=_('The tile content of this upload.'),
        blank=True,
        null=True,
    )
    description = models.TextField(
        _("Description"),
        help_text=_('The text content of this upload.'),
        blank=True,
        null=True
    )

    #
    #  FUNCTIONS
    #

    def __str__(self):
        return str(self.pk)
```

Append your ``foundation/models/__init__.py`` file with the following code

```python
from foundation.models.url_request_file_upload import URLRequestFileUpload
```

Append your ``foundation/admin.py`` file with the following code

```python
admin.site.register(URLRequestFileUpload)
```


## Step 2: API View

Inside your ``api/views.py`` file please append the following code:

```python
from api.serializers import URLRequestFileUploadSerializer


class URLRequestFileUploadAPIView(views.APIView):
    def post(self, request):
        serializer = URLRequestFileUploadSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()

        return response.Response( # Renders to content type as requested by the client.
            status=status.HTTP_200_OK,
            data={
                'detail': serializer.data,
            }
        )
```


## Step 3: urls.py

Inside your ``api/urls.py`` file please append the following code:

```python
path('api/url-request-file-uploads', views.URLRequestFileUploadAPIView.as_view()),
```


## Step 4: Serializer

Inside your ``api`` app, please create a ``serializers`` folder.

Inside youre ``api/serializers`` folder, please create a ``__init__.py`` file with the following contents:

```python
from api.serializers.url_request_file_upload_serializers import URLRequestFileUploadSerializer
```

Inside your ``api/serializers/url_request_file_upload_serializers.py`` file please append the following code:

```python
# -*- coding: utf-8 -*-
from django.db import transaction
from django.db.models import Q, Prefetch
from django.utils.translation import ugettext_lazy as _
from django.utils import timezone
from django.utils.http import urlquote
from rest_framework import exceptions, serializers
from rest_framework.response import Response
from rest_framework.validators import UniqueValidator
from foundation.models import URLRequestFileUpload


from django.core.files.base import ContentFile
import base64
import six
import uuid
def get_content_file_from_base64_string(data, filename):
    """
    Function will convert the string and filename parameter and return a
    `ContentFile` object.

    Special thanks:
    (1) https://github.com/tomchristie/django-rest-framework/pull/1268
    (2) https://stackoverflow.com/a/39587386
    """
    # Check if this is a base64 string
    if isinstance(data, six.string_types):
        # Check if the base64 string is in the "data:" format
        if 'data:' in data and ';base64,' in data:
            # Break out the header from the base64 content
            header, data = data.split(';base64,')

        # Try to decode the file. Return validation error if it fails.
        try:
            decoded_file = base64.b64decode(data)
        except TypeError:
            print("Failed conversion.")
            return None

        # Convert it to the content file.
        data = ContentFile(decoded_file, name=filename)
    return data

class URLRequestFileUploadSerializer(serializers.Serializer):
    attached_file_name = serializers.CharField(write_only=True, allow_null=False,)
    attached_file = serializers.CharField(write_only=True, allow_null=False,)
    title = serializers.CharField(required=True, allow_null=False,)
    description = serializers.CharField(required=True, allow_null=False,)

    @transaction.atomic
    def create(self, validated_data):
        """
        Override the `create` function to add extra functinality.
        """

        # Extract our upload file data
        content = validated_data.get('attached_file')
        filename = validated_data.get('attached_file_name')

        # Convert the base64 data into a `ContentFile` object.
        content_file = get_content_file_from_base64_string(content, filename)

        # Create our file.
        private_file = URLRequestFileUpload.objects.create(
            title = validated_data.get('title'),
            description = validated_data.get('description'),
            data_file = content_file, # When you attack a `ContentFile`, Django handles all file uploading.
        )

        # Return our validated data.
        return private_file
```

## Step 5: HTTPie

We are going to to upload a file using the ``http`` command in your console. Special thanks to the following link:

* [Stackoverflow](https://stackoverflow.com/a/52412141)

In your terminal please run the following on any sample file. This code will convert the file into a `base64` file. Begin by creating a sample file:

```bash
cat > urls.csv
```

And populate it with some data:

```bash
url
http://google.com
http://duckduckgo.com
```

Afterwords conver this data to our ``base64`` file format:

```bash
openssl base64 -in urls.csv > urls.csv.base64
```

Afterwords, we will upload our ``base64`` file into our API endpoint. Please the following in your terminal:

```bash
http POST :8000/api/url-request-file-uploads attached_file_name=urls.py attached_file=@urls.csv.base64 title="My Tech Blogs" description="The tech blogs I want to have a record of."
```
