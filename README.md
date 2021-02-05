###dj_rest_auth

info repos:
https://gist.github.com/dgilge/dbe9260208aadee535cef7c412a1162e
https://michaeldel.github.io/posts/django-rest-auth-social-tutorial/
https://github.com/michaeldel/django-rest-auth-social-example/blob/master/app/urls.py
https://simpleisbetterthancomplex.com/tutorial/2018/11/22/how-to-implement-token-authentication-using-django-rest-framework.html
https://medium.com/@MicroPyramid/integration-of-github-api-with-python-django-6f4253f9deeb
https://www.toptal.com/django/integrate-oauth-2-into-django-drf-back-end
https://jpadilla.github.io/django-rest-framework-oauth/authentication/

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
        
![image](https://user-images.githubusercontent.com/59927776/107033631-e265dc80-67b5-11eb-874d-664da7fc5118.png)

We can now fill our new Social Application, do not forget to add the default website (example.com in our case) to Sites field:
![image](https://user-images.githubusercontent.com/59927776/107040329-5fe21a80-67bf-11eb-895a-019c1de0e510.png)



