---
layout: page
title: Kurzy
category_type: 'Kurzy'
---
{% assign category_list = site.custom-categories-courses %}

{% include list-categories.html lang=page.lang %}
{% include list-posts.html lang=page.lang %}