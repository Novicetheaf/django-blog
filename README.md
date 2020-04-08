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

                    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('-published_date')
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

                    from django.conf.urls import url, include
                    from django.contrib import admin
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

## Create Your Base Templates And CSS Styles 

It loads all your external CSS and JS links and your HTML code in this file will render your nav bar. Any other HTML files you create (e.g. blogposts.html) are extensions of your base.html file.

You create a base.html and use {% extends 'base.html' %} to render any subsequent html files (e.g. blogposts.html) to the browser.

- create a new folder called 'templates' at the base of your project

    - inside templates create a file called base.html

        base.html:
                   
                    {% load static from staticfiles %}
                    <!DOCTYPE html>
                    <html lang="en">
                    <head>
                        <meta charset="UTF-8">
                        <meta name="viewport" content="width=device-width, initial-scale=1.0">
                        <meta http-equiv="X-UA-Compatible" content="ie=edge">
                        <title>EOC - Blog</title>
                        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootswatch/3.3.7/cerulean/bootstrap.min.css">
                        <link rel="stylesheet" href="{% static 'css/custom.css' %}">
                        <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
                        <script src="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/js/bootstrap.min.js"></script>
                    </head>
                    <body>
                        <!-- Fixed masthead -->
                            <nav class="navbar navbar-masthead navbar-default navbar-fixed-top">
                                <div class="container">
                                    <div class="navbar-header">
                                        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar"
                                                aria-expanded="false" aria-controls="navbar">
                                            <span class="sr-only">Toggle navigation</span>
                                            <span class="icon-bar"></span>
                                            <span class="icon-bar"></span>
                                            <span class="icon-bar"></span>
                                        </button>
                                        <a class="navbar-brand" href="{% url 'get_posts' %}"><img src="/media/img/logo.png"></a>
                                        </div>
                                    <div id="navbar" class="navbar-collapse collapse">
                                        <ul class="nav navbar-nav navbar-right">
                                            <li><a href="{% url 'new_post' %}">New Post</a></li>
                                            </ul>
                                    </div>
                                </div>
                            </nav>

                            <div id="masthead">
                                <div class="container">
                                    <div class="row">
                                        <h1> stack
                                            <p id="logo" class="lead">The Trials of a Bootcamp Developer</p>
                                        </h1>
                                    </div>
                                </div>
                            </div>
                        
                            <div class="container">
                                <div class="row">
                                    <div class="col-md-12">
                                        <div class="panel">
                                            <div class="panel-body">
                                                <!--blogpost entries-->
                                                {% block content %}
                                                {% endblock %}
                                                <!--blogpost entries-->
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <footer>
                                <div class="container">
                                    <p>Made by Code Institute bootcampers</p>
                                </div>
                            </footer>
                            
                    </body>
                    </html>
                    
- static folder:

        css:

            -create new file called custom css

- custom.css:

            .navbar-form input, .form-inline input {
                width: auto;
            }

            body {
                font-family: Arial;
                padding-top: 50px;
            }

            footer {
                margin-top: 40px;
                padding-top: 40px;
                padding-bottom: 40px;
                background-color: #ededed;
            }

            h1, h1 a {
                color: #555555;
                text-decoration: none;
            }

            #masthead {
                padding-left:10px;
                min-height: 199px;
            }

            #masthead h1 {
                font-size: 55px;
                line-height: 1;
                margin-top: 50px;
            }

            #masthead .well{
                margin-top: 31px;
                min-height: 127px;
            }

            .navbar.affix {
                position: fixed;
                top: 0;
                width: 100%;
            }

            .navbar-brand{
                padding: 10px 10px;
            }

            #logo {
                color: #12AB82;
            }

            .story-img {
                margin-top: 25%;
                display: block;
            }

            a, a:hover {
                color: #223344;
                text-decoration: none;
            }

            .icon-bar {
                background-color: #fff;
            }

            .dropdown-menu {
                min-width: 250px;
            }

            .panel {
                border-color: transparent;
                border-radius: 0;
            }

            .thumbnail {
                margin-bottom: 8px;
            }

            .img-container {
                overflow: hidden;
                height: 170px;
            }

            .img-container img {
                min-width: 280px;
                min-height: 180px;
                max-width: 380px;
                max-height: 280px;
            }

            .img-circle{
                width:100px;
                height: 100px; 
            }

            .img-blogpost{
                height: 300px;
                width: auto;
            }

            .txt-container {
                overflow: hidden;
                height: 100px;
            }

            .panel .lead {
                overflow: hidden;
                height: 90px;
            }

            .label-float {
                margin: 0 auto;
                position: absolute;
                top: 0;
                z-index: 1;
                width: 100%;
                opacity: .9;
                padding: 6px;
                color: #fff;
            }

            .boldtext{
                font-weight: bold;
            }

            @media screen and (min-width: 768px) {
                #masthead h1 {
                    font-size: 80px;
                }
            }

- in posts folder create a template folder: 

    - create blogposts.html:

                    {% extends 'base.html' %}
                        {% block content %}
                            {% for post in posts %}
                                <div class="row">
                                    <div class="col-md-2 col-sm-3 text-center">
                                        <a class="story-img" href="#">
                                            <img src="/media/img/profile.jpg" class="img-circle">
                                        </a>
                                        <p><span class="boldtext">Author:</span> Niel</p>
                                    </div>
                                    <div class="col-md-10 col-sm-9">
                                        <h3>{{ post.title }}</h3>
                                        <div class="row">
                                            <div class="col-xs-9">
                                                <p>{{ post.content|truncatewords:30 }}</p>
                                                <p><a href="{% url 'post_detail' post.id %}"  class="btn btn-default">Read more</a></p>
                                                <p><span class="boldtext">Published on:</span> {{ post.published_date }} </p>
                                                <p><span class="boldtext">Views:</span> {{post.views}}</p>
                                                <p><span class="boldtext">Tag:</span> {{ post.tag }}</p>
                                            </div>
                                        </div>
                                    </div>
                                    <hr>
                                </div>
                            {% endfor %}
                        {% endblock %}

    - create postdetail.html:

            {% extends "base.html" %}
            {% block content %}
                <div class="row">
                    <div class="col-md-2 col-sm-3 text-center">
                        <a class="story-img" href="#">
                            <img src="/media/img/profile.jpg" class="img-circle">
                        </a>
                        <p><span class="boldtext">Author:</span> Niel</p>
                    </div>
                    <div class="col-sm-10 col-md-9">
                        {% if post.image %}
                            <img class="img-blogpost" src="/media/{{ post.image }}">
                        {% endif %}
                        <h3>{{ post.title }}</h3>
                        <div class="row">
                            <div class="col-xs-9">
                                <p>{{ post.content }}</p>
                                <p><span class="boldtext">Published on:</span> {{ post.published_date }} </p>
                                <p><span class="boldtext">Views:</span> {{post.views}}</p>
                                <p><span class="boldtext">Tag:</span> {{ post.tag }}</p>
                                <a href="{% url 'get_posts' %}" class="btn btn-default">Back to Blog</a>
                                <a href="{% url 'edit_post' post.id %}" class="btn btn-default">Edit Post</a>
                                <hr>
                            </div>
                        </div>
                    </div>
                </div>
            {% endblock %}

<hr>

[![Build Status](https://travis-ci.org/Novicetheaf/django-blog.svg?branch=master)](https://travis-ci.org/Novicetheaf/django-blog)