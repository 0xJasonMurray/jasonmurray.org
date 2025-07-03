---
title: "Django Quick Start"
date: 2020-11-21T16:56:15-06:00
toc: false
images:
categories:
  - tech
tags: 
  - django
  - quickstart
---
# Overview

Django is a high-level Python web development framework.  To put it simply, it eliminates the complexity of building a Python enable website.



# Details 

Create a directory for the Django project:

*Caution*: Don't use `django` as the directory name.  

```
mkdir dproject
cd dproject
```

Setup a Python virtual environment:

```
python -m venv .venv
```

Activate virtual env:

```
source .venv/bin/activate
```

Install Django:

```
pip install django
```

Create a new Django project:

```
django-admin startproject mysite
```

Start development server:

```
cd mysite
python manage.py runserver
```

Test the development server is operating properly:

```
jemurray@mbp-2019:~ $ elinks -dump http://localhost:8000
[1]django

   View [2]release notes for Django 3.1

The install worked successfully! Congratulations!

   You are seeing this page because [3]DEBUG=True is in your settings file
   and you have not configured any URLs.
```

Create the first app within the project:

```
cd myapp
./manage.py startapp myapp
```

Start writing code...