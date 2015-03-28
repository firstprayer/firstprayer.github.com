---
layout: post
title: "5 Most Useful Small Tools in Python"
description: ""
category: 
tags: []
published: true
comment: true
---

The main reason why I love Python is because it's handy for a wide range of jobs. You can almost always find libraries that satisfy your requirement. Here're some very useful Python libraries/tools I found during my daily *Pythoning*

### 1.  [PrettyTable](https://code.google.com/p/prettytable/)

PrettyTable can structure your command line outputs into clean and neat table format, like this:

{% highlight sh %}
+-----------+------+------------+-----------------+
| City name | Area | Population | Annual Rainfall |
+-----------+------+------------+-----------------+
| Adelaide  | 1295 |  1158259   |      600.5      |
| Brisbane  | 5905 |  1857594   |      1146.4     |
| Darwin    | 112  |   120900   |      1714.7     |
| Hobart    | 1357 |   205556   |      619.5      |
| Melbourne | 1566 |  3806092   |      646.9      |
| Perth     | 5386 |  1554769   |      869.4      |
| Sydney    | 2058 |  4336374   |      1214.8     |
+-----------+------+------------+-----------------+
{% endhighlight %}

It comes with a lot of options, such as alignment, sorting, etc.

### 2.  [Matplotlib](http://matplotlib.org/)

Python is handy for data processing. And with Matplotlib, Python can also be awesome at visualization! This tool provides  visualization interface similar to Matlab, allow users to draw & save figures conveniently

<img src="/images/surface3d_demo.hires.png" style="width:50%" />

### 3.  [Python-Excel](http://www.python-excel.org/)

This library actually contains different libraries for interacting with Excel file (reading, writing, etc). For data process, it's a super useful tool since many data files come in the format of .xlsx or other excel format. 

### 4.  [Requests](http://docs.python-requests.org/en/latest/)

From my experience this is the best web client library in Python I've ever used, for two reasons:

1.  It's powerful
2.  It's just ... easy and simple to use

The best part I love about it is it supports session. That means I can use it to emulate a logged-in client so as to test the website I develop. 

### 5.  [MonSQL](http://monsql.readthedocs.org/en/latest/)

This is a light-weight SQL wrapper for Python. It supports MySQL, SQLite3 and PostgreSQL for now. It's design to be easy to use, so people don't need to put a lot of SQL query in their code, and can make the code much better. Example of usage:

{% highlight python %}
user_table = monsql.get('user')
activated_users = user_table.find({'state': 2})
for user in users:
	print user.id, user.name, user.state
{% endhighlight %}
