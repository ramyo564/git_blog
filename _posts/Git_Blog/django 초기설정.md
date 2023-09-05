
```python
python -m venv venv
python.exe -m pip install --upgrade pip
python install django
django-admin startproject core .
pip install black
pip install flake8
pip install djangorestframework
pip install drf-spectacular

python manage.py startapp pages
```

## DRF 초기설정

https://www.django-rest-framework.org/

```
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```


```
REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```

## drf-spectacular

https://drf-spectacular.readthedocs.io/en/latest/

```
INSTALLED_APPS = [
    # ALL YOUR APPS
    'drf_spectacular',
]
```

```
REST_FRAMEWORK = {
    # YOUR SETTINGS
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}
```

```
SPECTACULAR_SETTINGS = {
    'TITLE': 'Your Project API',
    'DESCRIPTION': 'Your project description',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
    # OTHER SETTINGS
}
```