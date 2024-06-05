django-sql-middleware
==================
A basic middleware tool designed for analyzing the execution of SQL queries in Django's Object-Relational Mapping (ORM).

# Usage
Include the following line of code in the middleware section of your project's settings.py file.
```
django-sql-middleware.middleware.new_middleware
```

# How it works

What is the mechanism behind its functioning?
The tool utilizes Django's inherent functionalities to examine the SQL generated from queries conducted by the application. External tools are utilized to format and emphasize the SQL code displayed in the terminal window.

# Further Reads

[Django Roadmap](./roadmaps/django_roadmap.md)
[Django Middleware Roadmap](./roadmaps/middleware_roadmap.md)