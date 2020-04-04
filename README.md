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


<hr>

[![Build Status](https://travis-ci.org/Novicetheaf/django-blog.svg?branch=master)](https://travis-ci.org/Novicetheaf/django-blog)