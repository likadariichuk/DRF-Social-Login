###dj_rest_auth

    # Create the project directory
    mkdir tutorial
    cd tutorial

    # Create a virtual environment to isolate our package dependencies locally
    python3 -m venv env
    source env/bin/activate  # On Windows use `env\Scripts\activate`

    # Install Django and Django REST framework into the virtual environment
    pip install django
    pip install djangorestframework

    # Set up a new project with a single application
    django-admin startproject api .  # Note the trailing '.' character
    cd api
    django-admin startapp authentication
    cd ..

    python manage.py migrate
    python manage.py createsuperuser --email admin@example.com --username admin
    
  
    pip install 'dj-rest-auth[with_social]'

##looool

###settings.py
        
        INSTALLED_APPS = [
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
           
            'rest_framework',
            'rest_framework.authtoken',
            'dj_rest_auth',
        ]
        
        REST_FRAMEWORK = {
            'DEFAULT_AUTHENTICATION_CLASSES': (
                'rest_framework.authentication.TokenAuthentication',
            )
        }
        
 urls.py:
        
        from django.urls import path, include
        
        urlpatterns = [
            ...,
            path('auth/', include('dj_rest_auth.urls'))
        ]

        python manage.py migrate
        
  social auth settings.py:      
  
        INSTALLED_APPS = [
            'django.contrib.sites',
            'allauth',
            'allauth.account',
            'allauth.socialaccount',
            'dj_rest_auth.registration',
            
            'allauth.socialaccount.providers.github',
        ]

        SITE_ID = 1
        
        python manage.py migrate
