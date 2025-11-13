---
layout: home
---

{% include header/cta.html %}
{% include home/hero.html %}
{% include home/cards.html %}

<main id="main" class="container-wide" style="padding: 1rem 0 3rem">
  <h2>Recent posts</h2>
  <div class="posts-grid">
    {% for post in site.posts limit:9 %}
      <article class="post-card">
        <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
        <div class="post-meta">{{ post.date | date: "%b %d, %Y" }}
        {% if post.tags %} ·
          {% for tag in post.tags %}
            <span class="tag">{{ tag }}</span>
          {% endfor %}
        {% endif %}
        </div>
        <p>{{ post.excerpt }}</p>
        <p><a class="btn btn-ghost" href="{{ post.url }}">Read more</a></p>
      </article>
    {% endfor %}
  </div>
</main>

{% if site.data.variables.default.paths.rss %}
<div class="container" style="text-align:center;padding-bottom:2rem">
  <a href="{{ site.data.variables.default.paths.rss }}">RSS</a>
  · <a href="{{ site.baseurl }}/archive.html">Archive</a>
  · <a class="btn btn-primary" href="mailto:{{ site.email | default: 'joseph@verron.pro' }}?subject=Consulting%20inquiry">Hire me</a>
  </div>
{% endif %}

{% include footer/custom.html %}
