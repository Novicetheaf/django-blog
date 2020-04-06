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


- posts:

        views.py:

                from django.shortcuts import render, get_object_or_404, redirect
                from django.utils import timezone
                from .models import Post
                from .forms import BlogPostForm

                def get_posts(request):
                    """
                    Create a view that will return a list
                    of Posts that were published prior to 'now'
                    and render them to the 'blogposts.html' template
                    """

                    posts = Post.object.filler(published_date__lte=timezone.now
                        ()).order_by('-published_date')
                    return render(request, "blogposts.html", {'posts': posts})

                def post_detail(request, pk)
                    """
                    Create a view that returns a single
                    Post object based on the post ID (pk) and
                    renders it to the 'postdetail.html' template.
                    Or return a 404 error if the post is
                    not found
                    """

                    post = get_object_or_404(Post, pk=pk)
                    post.views += 1
                    post.save()
                    return render(request, "postdetail.html", {'post': post})

                def create_or_edit_post(request, pk=None):
                    """
                    Create a view that allows us to create
                    or edit a post depending on if the Post
                    ID is null or not
                    """

                    post = get_object_or_404(Post, pk=pk) if pk else None
                    if request.method == "POST":
                        form = BlogPostForm(request.POST, request.FILES, instance=post)
                        if form.is_valid():
                            post = form.save()
                            return redirect(post_detail, post.pk)

                    else:
                        form = BlogPostForm(instance=post)
                    return render(request, 'blogpostform.html', {'form': form})
                    
## Create Your URLs 

- blog:
        urls.py:

            import:

                    from django.views.generic import RedirectView
                    from django.views.static import serve
                    from .settings import MEDIA_ROOT


            urlpatterns = [
                url(r'^$', RedirectView.as_view(url='posts')),
                url(r'posts/', include('posts.urls')),
                url(r'^media/(?P<path>.*)$', serve, {'document_root': MEDIA_ROOT}),
            ]

- posts: Create file urls.py

        urls.py:
                from django.conf.urls import url
                from .views import get_posts, post_detail, create_or_edit_post

                urlpatterns = [
                    url(r'^$', get_posts, name='get_posts'),
                    url(r'^(?P<pk>\d+)/$', post_detail, name='post_detail'),
                    url(r'^new/$', create_or_edit_post, name='new_post'),
                    url(r'^(?P<pk>\d+)/edit/$', create_or_edit_post, name='edit_post')
                ]


<hr>

[![Build Status](https://travis-ci.org/Novicetheaf/django-blog.svg?branch=master)](https://travis-ci.org/Novicetheaf/django-blog)