---
layout: default
---

{% for post in site.posts %}
# [ {{ post.title }} ]({{ post.url | relative_url }})
#### {{ post.date | date_to_string }} - {% if post.author %} {{ post.author }} {% else %} atrodo {% endif %}
  {{ post.content }}
  {% unless forloop.last %}
  ---
  {% endunless %}
{% endfor %}
