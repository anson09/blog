---
layout: home
---

Working / Exploring footprints, most of them keep updated with my knowledge.
[Original creativity](/LICENSE.txt) Respect

# Last Post

{% assign mypages = site.pages | sort: "order" | reverse %}
{% for page in mypages %}
{% if page.order %}

- [{{ page.title }}]({{ page.url | absolute_url }}) <sub>`{{page.dir | slice: 1,4}}`</sub>

{% endif %}
{% endfor %}
