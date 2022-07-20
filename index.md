---
layout: home
---

_Working and learning footprints. Most of them keep updated with my knowledge._

# Last Post

{% assign mypages = site.pages | sort: "order" | reverse %}
{% for page in mypages %}
{% if page.order %}

- [{{ page.title }}]({{ page.url | absolute_url }}) <sub>`{{page.dir | slice: 1,4}}`</sub>

{% endif %}
{% endfor %}
