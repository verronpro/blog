---
layout: home
---

# Posts
{% for post in site.posts limit:5 %}
## [{{ post.title }}]({{ post.url }})
{{ post.excerpt }}

---

{% endfor %}