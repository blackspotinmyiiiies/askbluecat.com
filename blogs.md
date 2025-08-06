---
layout: default
title: BlueCat's Cybersecurity Blogs
permalink: /blogs/
---

# BlueCat's Cybersecurity Blogs - Meow! 🐱

Meow meow! Here are all of BlueCat's wisdom posts about cybersecurity, meow:

<div class="mt-6">
{% for post in site.posts %}
  <article class="mb-8 p-4 border-l-4 border-blue-500">
    <h2 class="text-xl font-semibold">
      <a href="{{ post.url | relative_url }}" class="text-blue-600 hover:text-blue-800">{{ post.title }}</a>
    </h2>
    <p class="text-gray-600 text-sm mt-1">{{ post.date | date: "%B %d, %Y" }}</p>
    <div class="mt-2 text-gray-700">
      {{ post.excerpt }}
    </div>
    <a href="{{ post.url | relative_url }}" class="text-blue-600 hover:text-blue-800 text-sm">Read more meow →</a>
  </article>
{% endfor %}
</div>

{% if site.posts.size == 0 %}
<p class="text-gray-600 mt-4">Meow! No blog posts yet. BlueCat is still sharpening her claws and preparing cybersecurity wisdom, meow meow!</p>
{% endif %}
