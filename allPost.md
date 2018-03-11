---
layout: default
title: Articles 14
sidebar_link: true
---


<div class="content">
	
	
	
	
  {% include pagination-newer.html %}

  


  {% for post in paginator.posts %}
  <article class="post-body">
    <h2 class="post-title">
      <a href="{{ site.baseurl }}{{ post.url }}">
        {{ post.title }}
      </a>
    </h2>
    {% include post-meta.html post=post %}



    {% if post.excerpt %}
      {% comment %}Excerpt may be equal to content. Check.{% endcomment %}
      {% capture content_words %}
        {{ post.content | number_of_words }}
      {% endcapture %}
      {% capture excerpt_words %}
        {{ post.excerpt | number_of_words }}
      {% endcapture %}


    {% endif %}
  </article>
  {% endfor %}

  {% include pagination-older.html %}
</div>



