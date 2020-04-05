# Django Blog Mini-Project

**A simple blog app written using Django**

<hr>

## Setup Github & Travis

you configure Travis to perform continuous integration testing.

- $ pip3 install django==1.11.24

- $ pip3 freeze > requirements.txt

- $ django-admin startproject blog .

- $ python3 manage.py migrate

- Now Test the page: 

    $ python3 manage.py runserver

- May need to add this:

 ALLOWED_HOSTS = [
    'localhost'
]

- $ git init

- $ echo -e "*.sqlite3\n*.pyc"\n__pycache__/" > .gitignore

- Create file: travis.yml:

        language: python
        python:
            - "3.4"
        install: "pip install -r requirements.txt"
        script:
        - SECRET_KEY="whatever" python manage.py test


## Django - Directories

This directory structure will serve your templates, media files and static files when your project is run locally or deployed to a live site.


- $ python3 manage.py startapp posts

    - Inside posts app/folder:

            - Create templates folder


- Create these folders:

    static:

            css
            img
            js

- Create this folder:

    media:

            img

- blog folder:

        settings.py:

                INSTALLED_APPS = [
                    'posts'
                ]

                 TEMPLATES = [
                    
                 Edit this: 'DIRS': [os.path.join(BASE_DIR, 'templates')],
                    
                 Add this:  'django.template.context_processors.media',

                ]


                STATIC_URL = '/static/'
                STATICFILES_DIRS = (os.path.join(BASE_DIR, 'static'), )

                MEDIA_URL = '/media/'
                MEDIA_ROOT = os.path.join(BASE_DIR, 'media')


## Create Your Data Models And Forms

You create your Post model in models.py and you create your BlogPostForm in forms.py.

- Models.py:

    - import: 
            from django.db import models
            from django.utils import timezone

    - Add class:

            class Post(models.Model):
                """
                A single blog post
                """
                title = models.CharField(max_length=200)
                content = models.TextField()
                created_date = models.DateTimeField(auto_now_add=True)
                published_date = models.DateTimeField(blank=True, null=True, default=timezone.now)
                views = models.IntegerField(default=0)
                tag = models.CharField(max_length=30, blank=True, null=True)
                image = models.ImageField(upload_to="img", blank=True, null=True)

                def __unicode__(self):
                    return self.title


- Create file forms.py in posts folder

        forms.py:

                from django import forms
                from .models import Post

                class BlogPostForm(forms.ModelForm):
                    class Meta:
                        model = Post
                        fields = ('title', 'content', 'image', 'tag', 'published_date')


- admin.py in posts folder

        from django.contrib import admin
        from models import Post

        admin.site.register(Post)


- Cli commands:

       $ pip3 install pillow

       $ pip3 freeze > requirements.txt

       $ python3 manage.py makemigrations

## Create your views

 Creating the get_posts and create_or_edit_post in the views.py file of our posts app.



<hr>

[![Build Status](https://travis-ci.org/Novicetheaf/django-blog.svg?branch=master)](https://travis-ci.org/Novicetheaf/django-blog)