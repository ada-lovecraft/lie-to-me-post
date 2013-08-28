---
layout: page
title: Words 
tagline: words words
---
{% include JB/setup %}

{% for post in site.posts %}
## [{{post.title}}]({{post.url}})
{{post.excerpt}}
{% endfor %}

