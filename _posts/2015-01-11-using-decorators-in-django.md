---
layout: post
title: "Using Python Decorators in Web Development"
description: ""
category: 
tags: []
---

*Decorator* is a very useful technique for dynamically alter the functionality of a function, method or class. It also allows us to write reusable code in a very elegant way. In this post I'll discuss the usage of decorators in web development. 

### 1. What is decorator? 

There's a [brilliant post](https://pythonconquerstheuniverse.wordpress.com/2012/04/29/python-decorators/) about this. To summarize the content from this post:

>   A decorator is a function that takes a function object as an argument, and returns a function object as a return value.

An example:

{% highlight python %}
def verbose(original_function):
    # make a new function that prints a message when original_function starts and finishes
    def new_function(*args, **kwargs):
        print("Entering", original_function.__name__)
        original_function(*args, **kwargs)
        print("Exiting ", original_function.__name__)
 
    return new_function
{% endhighlight %}

The way to use a decorator is:

{% highlight python %}
def some_func():
    ...

some_func = verbose(some_func)
{% endhighlight %}

Or, use the syntax super provided by Python, just:

{% highlight python %}
@verbose
def some_func():
    ...
{% endhighlight %}

### 2. An example

Now let's see how to use decorators to make our code more elegant, and more reusable. The example is based on [Django](https://www.djangoproject.com/)

Suppose we have a web controller(or *view*):

{% highlight python %}
def update_article(request, article_id):
    # 0. Check the request method. This controller should only allow POST
    if not request.method == 'POST':
        return HttpResponseNotAllowed(['POST'])

    # 1. Check if the user is log on
    if 'user' not in request.session:
        return HttpResponse('Unauthorized', status=401)
    user = get_log_on_user(requset)
    if user is None or user.role != 'writer':
        return HttpResponseNotAllowed()

    # 2. Check if the article exists
    article = Article.objects.filter(id=int(article_id))
    if not article.exists():
        return HttpResponseNotFound('Article Not found')
    article = article[0]

    # 3. Check if the user is the author
    if not article.author_id == user.id:
        return HttpResponseNotAllowed('Open allowed for author')

    # 4. Let's do the business!
    # Blah, blah, blah, update the article
    ......

    # 5. Finally, response with json format data
    return HttpResponse(json.dumps({'success': True, 'article': {'id': article.id}}))
{% endhighlight %}

Now, suppose we want to add several new controlers, such as:

{% highlight python %}
def delete_article(request, article_id):
    # 0. Check the request method. This controller should only allow DELETE
    # 1. Check if the user is log on
    # 2. Check if the article exists
    # 3. Check if the user is the author
    # 4. Delete the article
    # 5. Response with json format data
    ...

def publish_article(request, article_id):
    # 0. Check the request method. This controller should only allow POST
    # 1. Check if the user is log on
    # 2. Check if the article exists
    # 3. Check if the user is the author
    # 4. Publish the article
    # 5. Response with json format data
    ...

...
{% endhighlight %}

It's obvious that the first several steps of these controllers are the same. How can we write reusable code for this? We can do this by writing *decorators*! Here's how:

We write a decorator <code>writers_required</code>:

{% highlight python %}
def writers_required(controller):
    """Require the user log on, and has 'writer' role"""
    def inner(*args, **kwargs):
        request = args[0]
        if 'user' not in request.session:
            return HttpResponse('Unauthorized', status=401)

        user = get_log_on_user(requset)
        if user is None or user.role != 'writer':
            return HttpResponseNotAllowed()

        return controller(*args, **kwargs)

    return inner
{% endhighlight %}

And change the update_article controller to:
    
{% highlight python %}
@writers_required
def update_article(request, article_id):
    ...

    # 1. No need to check if the user is log on
    user = get_log_on_user(request)

    ...
{% endhighlight %}

Similarly, we can implement other decorators:

{% highlight python %}
def allow_methods(methods):
    """Check the request method. If the request method is invalid, return HttpResponseNotAllowed"""
    methods = [method.upper() for method in methods]
    def decorator(controller):

        def inner(*args, **kwargs):
            request = args[0]
            if request.method not in methods:
                return HttpResponseNotAllowed(methods)

            return controller(*args, **kwargs)

        return inner
    return decorator
{% endhighlight %}

{% highlight python %}
def article_operation(author_required=False):
    """
    Require the controller has a parameter 'article_id'
    Require the article of this id exists
    If author_required is True, then require the log on user be the author
    """
    def decorator(controller):

        def inner(*args, **kwargs):
            request, article_id = args[0], kwargs['article_id']

            article = Article.objects.filter(id=int(article_id), deleted=0)
            if not article.exists():
                return HttpResponseNotFound('Article Not found')
            article = article[0]

            if author_required:
                if 'user' not in request.session:
                    return HttpResponse('Unauthorized', status=401)
                if request.session.get('user')['id'] != article.author_id:
                    return HttpResponseNotAllowed('Open allowed for author')

            return controller(*args, **kwargs)

        return inner

    return decorator
{% endhighlight %}

{% highlight python %}
def json_view(controller):
    """
    If the return result of this controller is a dict or list,
    dump it into json string and wrap as a HttpResponse
    """
    def inner(*args, **kwargs):
        res = controller(*args, **kwargs)
        if isinstance(res, list) or isinstance(res, dict):
            return HttpResponse(json.dumps(res, cls=ComplexEncoder))

        return res
    return inner
{% endhighlight %}

Now the update_article controller becomes:
  
{% highlight python %}  
@json_view
@article_operation(True)
@allow_methods(['POST'])
@writers_required
def update_article(request, article_id):
    user = get_log_on_user(requset)
    article = Article.objects.get(id=int(article_id))
    # Blah, blah, blah, update the article
    return {'success': True, 'article': {'id': article.id}}
{% endhighlight %}

See?! The function is extremely elegant now. For other similar controllers:

{% highlight python %}

@json_view
@article_operation(True)
@allow_methods(['DELETE'])
@writers_required
def delete_article(request, article_id):
    # Delete the article
    ...
    return {'success': True, 'article': {'id': article.id}}

@json_view
@article_operation(True)
@allow_methods(['POST'])
@writers_required
def publish_article(request, article_id):
    # Publish the article
    ...
    return {'success': True, 'article': {'id': article.id}}

{% endhighlight %}

From this very simple example, we can see that decorator is really a great tool for reusing codes. 




