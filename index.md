---
layout: home
---

<small>_Working / Exploring footprints, most of posts keep updated with my knowledge.
[Original creativity](/LICENSE.txt) Respect_</small>

# Last Post

{% assign mypages = site.pages | sort: "order" | reverse %}
{% for page in mypages %}
{% if page.order %}

- [{{ page.title }}]({{ page.url | absolute_url }})<sub> `{{page.dir | slice: 1,4}}` {% assign tags_array = page.tags | split: " " %}{% for tag in tags_array %}`#{{ tag }}` {% endfor %} </sub>

{% endif %}
{% endfor %}
