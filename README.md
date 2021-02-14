# Painless OAuth2 tutorial for Django Rest Framework #
Sometimes it may seem a chore to tear yourself away from your creative programming part to face the unglamorous and unexciting business of setting up the authentication.

Let's not be bothered and use a third-party package, aptly named [dj-rest-auth](https://github.com/iMerica/dj-rest-auth), which will make our day better.
## Create Django with Rest Framework project ##
Create the project directory:
    
    mkdir api
    cd api

Create a virtual environment to isolate our package dependencies locally:
    
    python -m venv env
    source env/bin/activate  # On Windows use `env\Scripts\activate`

Install Django and Django REST framework into the virtual environment:
    
    pip install django
    pip install djangorestframework

Set up a new project with a single application:
    
    django-admin startproject api 
    rename parent "api" directory to the "core"
    cd core
    cd api
    django-admin startapp authentication
    cd ..
    
    ![tree](https://user-images.githubusercontent.com/59927776/107851621-a95be680-6e0b-11eb-9754-0283bd17f848.png)
    
    python manage.py migrate
    python manage.py createsuperuser --email admin@example.com --username admin
    
  
    pip install 'dj-rest-auth[with_social]'

settings.py:
        
        INSTALLED_APPS = [
            ...,
            'rest_framework',
            'rest_framework.authtoken',
            ...,
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
            ...,
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

Add a new Social Application, do not forget to add the default website (example.com in our case) to Sites field:

![image](https://user-images.githubusercontent.com/59927776/107040329-5fe21a80-67bf-11eb-895a-019c1de0e510.png)

Because we want to login with GitHub, we have to define a specific view for it where we are going to POST the code from URL that github will give us.

First define the GitHub login view, deriving from generic social login view:

    from allauth.socialaccount.providers.github import views as github_views
    from allauth.socialaccount.providers.github.views import GitHubOAuth2Adapter
    from django.urls import reverse
    from rest_auth.registration.views import SocialLoginView


    class GitHubLogin(SocialLoginView):
        adapter_class = GitHubOAuth2Adapter
        client_class = OAuth2Client

        @property
        def callback_url(self):
            # use the same callback url as defined in your GitHub app, this url
            # must be absolute:
            return self.request.build_absolute_uri(reverse('github_callback'))

The callback property probably will render the callback url in a view. And also without it you get no field error.

urls.py:

    from api.authentication.views import GitHubLogin
    
    urlpatterns = [
        ...,
        path('auth/github/', GitHubLogin.as_view()) #use this link to POST your github code to login
    ]
  
We have to fix that missing callback view. Because our app is a back-end API only, we want our callback view to redirect the user to front-end, including query parameters. 
We have to define a github callback view which will redirect the github callback to frontend.


views.py:

    import urllib.parse

    from django.shortcuts import redirect

    def github_callback(request):
        params = urllib.parse.urlencode(request.GET)
        return redirect(f'https://frontend/auth/github?{params}')
 
 
 urlpatterns = [
    ...,
    path('auth/github/callback/', github_callback, name='github_callback'), #use the same callback url as defined in your GitHub app
]

![callback](https://user-images.githubusercontent.com/59927776/107885319-cf5fb480-6ef9-11eb-8200-998d1ecef8c0.png)

We now need to retrieve the OAuth2 authorize URL, in our case it should look (almost) like this:

https://github.com/login/oauth/authorize?&client_id=6d2098b0d015605ff492
We could of course generate this address on front-end, but it is a much better idea to retrieve it from back-end. Fortunately, such view already exists wihtin Django AllAuth module:

from allauth.socialaccount.providers.github import views as github_views

urlpatterns = [
    ...,
    path('auth/github/url/', github_views.oauth2_login)
]

This view will automatically redirect the user to authorize URL, with reverse('github_callback') as callback (hence the GitHubLogin URL name above), as you can see here (removed non-relevant output):

Find the last paragraph here (that's where you finished last time):
https://michaeldel.github.io/posts/django-rest-auth-social-tutorial/

info repos:
https://gist.github.com/dgilge/dbe9260208aadee535cef7c412a1162e
https://michaeldel.github.io/posts/django-rest-auth-social-tutorial/
https://github.com/michaeldel/django-rest-auth-social-example/blob/master/app/urls.py
https://simpleisbetterthancomplex.com/tutorial/2018/11/22/how-to-implement-token-authentication-using-django-rest-framework.html
https://medium.com/@MicroPyramid/integration-of-github-api-with-python-django-6f4253f9deeb
https://www.toptal.com/django/integrate-oauth-2-into-django-drf-back-end
https://jpadilla.github.io/django-rest-framework-oauth/authentication/
