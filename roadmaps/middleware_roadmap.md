### Introduction

Django middleware is a mechanism for universally modifying requests and responses. In other words, middlewares are tiers of business logic that are applied to all requests and responses. 

Middleware is a framework of hooks into Django’s request/response processing. It’s a light, low-level “plugin” system for globally altering Django’s input or output.

Middleware is a standard Python class that must have at least one of these methods declared:
- process_view
  (This function is invoked every time Django receives a request and directs it to a view.)
- process_exception
  (This method is invoked if a view encounters an unhandled exception. Therefore, the process_exception function is called once the request has been received by the view and has been returned.)
- process_template_response
  (This method is only called if the resultant response contains a render() method, which indicates a template is being rendered.)
- process_request
- process_response

### Applications of Middleware

#### 1. Filtering Requests
Middleware can be developed to selectively exclude invalid or possibly harmful requests and promptly terminate their execution (often by generating an error response), therefore preventing them from advancing any further. An example of a contrived scenario is the Cross-Site Request Forgery (CSRF) middleware mentioned earlier, which selectively blocks requests originating from different domains. An alternative illustration may involve the implementation of an RBAC (Role-Based Access Control) middleware, which effectively restricts users from accessing or altering resources for which they lack authorization. Another illustration is the utilization of geo-blocking middleware, which selectively screens and denies requests originating from specific geographical regions.
#### 2. Injecting Data into Requests
Middleware can be employed to introduce supplementary data into the request, which can subsequently be utilized within the application. An example that illustrates this concept is Django's Authentication Middleware, which appends a user object to each legitimate request. Calling request.user provides a handy means for the view and other middleware to retrieve specific information about the authenticated user.
#### 3. Performing Logging, Analytics and Other Miscellaneous Tasks
Certain middleware do not directly alter the request or response, but instead utilize the information included inside them. Put simply, these middleware are designated as 'read-only'. Consider an analytics middleware that saves comprehensive information about incoming system requests, including user details, URL, timestamp, and other relevant data, in a database. Subsequently, this data will be scrutinized to discern valuable insights or patterns. A middleware of this nature would essentially extract the request content and generate or modify corresponding records in the database, before seamlessly allowing it to proceed. An additional illustration may be a utilization monitoring middleware that keeps track of the extent to which a user has depleted their allocated usage quota.
### How does Middleware work?
In order to utilize middleware in Django, it is necessary to specify a collection of middleware classes or methods within the MIDDLEWARE setting in your <a href="#">settings.py</a> file. The sequence of the middleware components in this list is significant, as they are implemented in a top-down manner for each request and in a bottom-up manner for each answer.

Subsequently, for every request, the ensuing processes will occur:

1. The request object will be transferred to the SecurityMiddleware, which will execute security checks and make adjustments to it.
2. The altered request object will be transferred to the SessionMiddleware, which will initialize or generate a session for the request.
3. The altered request object will be transferred to the CommonMiddleware, which will execute URL normalization and redirection on it.
4. The altered request object will be transferred to the CsrfViewMiddleware, which will authenticate or assign a CSRF token for the request.
5. The altered request object will be transferred to the AuthenticationMiddleware, which will establish a connection between a user and the request through sessions.
6. The altered request object will be transferred to the MessageMiddleware, which will manage flash messages for the request.
7. The altered request object will be transferred to the XFrameOptionsMiddleware, which will assign the X-Frame-Options header to the request.
8. The altered request object will be transferred to the view function that corresponds to the URL pattern of the request.
9. The view function will handle the request and generate a response object.
10. The XFrameOptionsMiddleware will receive the response object and make certain adjustments to it.
11. The altered response object will be returned to the MessageMiddleware, which will append any messages to the response cookies.
12. The altered response object will be returned to the AuthenticationMiddleware, where it will undergo further alterations.
13. The altered response object will be returned to the CsrfViewMiddleware, where it will undergo further adjustments.
14. The altered response object will be returned to the CommonMiddleware, which will make certain modifications to it.
15. The altered response object will be returned to the SessionMiddleware, which will either save or remove the session associated with the answer.
16. The altered response object will be returned to the SecurityMiddleware, where it will undergo further adjustments.
17. The ultimate response entity will be transmitted back to the web server and subsequently to the client.
    
![Middleware](https://docs.djangoproject.com/en/1.8/_images/middleware.svg "Hooks and application order")

Middleware has the ability to impact both the incoming request and the outgoing response during various phases of their processing.

### Custom Middleware

Django enables the creation of custom middleware components using either functions or classes. A middleware component is a function that accepts a request object as input and produces a response object as output. It may also invoke another middleware component or a view function. A middleware component has the capability to execute certain activities prior to or following the invocation of another middleware component or view function.

- Create a Python package named "middleware" by creating a folder with a __init__.py file inside.
- Create a file called custom_middleware.py and include a standard Python function or class within it.
- Middleware can be implemented either as a function or as a class with callable instances.


#### Function-Based Middleware
```py
def simple_middleware(get_response):
    # One-time configuration and initialization.

    def middleware(request):
        # Code to be executed for each request before
        # the view (and later middleware) are called.

        response = get_response(request)

        # Code to be executed for each request/response after
        # the view is called.

        return response

    return middleware
```
#### Class-Based Middleware 
```py
class ExampleMiddleware:

    def _init_(self, get_response):
        self.get_response = get_response

    def _call_(self, request):

        # Code that is executed in each request before the view is called

        response = self.get_response(request)

        # Code that is executed in each request after the view is called
        return response

    def process_view(request, view_func, view_args, view_kwargs):
        # This code is executed just before the view is called

    def process_exception(request, exception):
        # This code is executed if an exception is raised

    def process_template_response(request, response):
        # This code is executed if the response contains a render() method
        return response
```

To complete the process, you need to include your own middleware in the MIDDLEWARE list located in the <a href="#">settings.py</a> file.
```py
MIDDLEWARE = [
    …
    'example_app.middleware_directory.custom_middleware_file.CustomMiddleware_class',
]
```
