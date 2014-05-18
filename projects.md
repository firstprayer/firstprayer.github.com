---
layout: page
permalink: /projects/
title: 项目 / Projects -- CodePrayer
tagline: 我参与过的项目
modified: 5-18-2014
comments: true
---

{% for project in site.data.projects %}
<div>
	<h1>{{ project.name }}</h1>
	<p>{{ project.description }}</p>
	{% if project.project_url %}
	<p>
		Interested people can visit <a href="{{project.project_url}}">here</a>
	</p>
	{% endif %}
</div>
{% endfor %}