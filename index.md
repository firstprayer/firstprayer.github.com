---
layout: page
title: Just making fortune
tagline: Supporting tagline
---
{% include JB/setup %}

Welcome, visitors. My name is Taiyuan Zhang, but you can just cal me Taylor. This is my blog, recording my work, my study and my life.   
Facts about me: A senior student in Tsinghua University, China, majoring in Computer Software Engineering. I believe that **TECHNOLOGY MAKES A DIFFERENCE**. The dramatic changes around us motivated by progress of technology, especially computer science, have proved that. I may not be lucky enough to be another Mark Zuckberg, but I believe I could also make some real difference.   
OK, What else? I like music. I play guitar. I play basketball, and am trying to learn badminton. And I like movies, those like Butterfly Effect are my favorite. And books: well, science fiction would be my type, but I also like phylosophy.  

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>




