# Essential Django REST Framework
## Step 1: httpie

Please install globally the following useful application for making API calls from your terminal.

```bash
pip install httpie
```

Confirm the code works.

```bash
http https://www.google.com/
```

Would you like to know more? Please [vist the website](https://httpie.org/).

## Step 2: Project Setup
Create the project directory

```bash
mkdir burrow-back
cd burrow-back
```

Create a virtual environment to isolate our package dependencies locally

```bash
python3 -m venv env
source env/bin/activate  # On Windows use `env\Scripts\activate`
```

Install Django and Django REST framework into the virtual environment

```bash
pip install django
pip install djangorestframework
```

Set up a new project with our applications

```bash
django-admin startproject burrow
cd burrow
django-admin startapp foundation
django-admin startapp api
```

Migrate the database once the project has been setup.

```bash
python manage.py migrate
```

We'll also create an initial user named ``admin`` with a password of ``123password``. We'll authenticate as that user later in our example.

```bash
python manage.py createsuperuser --email admin@example.com --username admin
```

Modify our ``settings.py`` file.

```python
INSTALLED_APPS = [
    # ...

    # Add our code near the bottom...
    'foundation.apps.FoundationConfig',
    'api.apps.ApiConfig',
]

# ...

# Comment out the CsrfViewMiddleware
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# ...

# Django-REST-Framework
# https://github.com/encode/django-rest-framework

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        # PLACE AUTHENTICATION CLASSES HERE.
    ),
    'DEFAULT_FILTER_BACKENDS': (
        # PLACE FILTERING CLASSES HERE.
    ),
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
    ],
    'DEFAULT_PARSER_CLASSES': (
        'rest_framework.parsers.JSONParser',
        'rest_framework.parsers.FormParser',
        'rest_framework.parsers.MultiPartParser',
    ),
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 50
}

# QUESTION 1: Where can I learn more about the Django REST Framework settings?
# ANSWER 1: https://www.django-rest-framework.org/api-guide/settings/
```

Modify our ``urls.py`` file.

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('foundation.urls')),
    path('', include('api.urls')),
]
```

Create ``urls.py`` file in our apps:

```bash
touch burrow/foundation/urls.py
touch burrow/api/urls.py
```

Populate the `urls.py` file.

```python
from django.urls import path

from . import views

urlpatterns = [
    #TODO: Fill in.
]
```

## Step 3: Generic API Views

Inside of our ``api/urls.py`` file, write the following.

```python
"""
api/urls.py
"""
from django.urls import path
from rest_framework.urlpatterns import format_suffix_patterns
from api import views

urlpatterns = [
    path('hello', views.HelloAPIView.as_view()),
]

urlpatterns = format_suffix_patterns(urlpatterns)

# QUESTION 1: What is "format_suffix_patterns"?
# ANSWER: https://www.django-rest-framework.org/tutorial/2-requests-and-responses/#adding-optional-format-suffixes-to-our-urls
```

Inside our ``api/views.py`` file, write the following.

```python
from rest_framework import status, response, views


class HelloAPIView(views.APIView):

    def get(self, request):
        """
        The ``GET`` call will return a simple message.
        """
        return response.Response( # Renders to content type as requested by the client.
            status=status.HTTP_200_OK,
            data={
                'detail': 'Hello World!',
            }
        )

        # Question and Answers:
        # QUESITON 1: Where do I lookup our status codes?
        # ANSWER 1: https://www.django-rest-framework.org/api-guide/status-codes/#status-codes

    def post(self, request):
        # Do not use the code below, this is what we used in ``Django``.
        data = request.POST  # Only handles form data.  Only works for 'POST' method.

        # This is how we get unvalidated data from the request.
        data = request.data  # Handles arbitrary data.  Works for 'POST', 'PUT' and 'PATCH' methods.

        return response.Response( # Renders to content type as requested by the client.
            status=status.HTTP_200_OK,
            data={
                'details': data,
            }
        )
```

To call our GET API, we run the following.

```bash
http http://127.0.0.1:8000/hello Accept:application/json
```

To call our POST API, we run the following.

```bash
http --form POST http://127.0.0.1:8000/hello say="Welcome to my API"
```
