# Step 9: Django (in Conda Environment)

## Overview

Django is a high-level Python web framework for rapid development.

## Installation

```bash
# Make sure conda environment is activated
conda activate mcp-workshop

# Install Django
pip install django

# Install Django REST framework (for API development)
pip install djangorestframework
```

## Additional Django Packages

```bash
# Install common Django utilities
pip install django-environ     # Environment variables
pip install django-cors-headers # CORS handling
pip install django-extensions  # Development extensions
```

## PostgreSQL Integration

Update your Django settings to use PostgreSQL:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mcp_workshop',
        'USER': 'YOUR_USERNAME',
        'PASSWORD': '',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

## Verification

```bash
# Make sure environment is activated
conda activate mcp-workshop

# Check Django version
python -m django --version

# Create a test project to verify installation
django-admin startproject test_project
cd test_project
python manage.py check
cd ..
rm -rf test_project
```

You should see "System check identified no issues."
