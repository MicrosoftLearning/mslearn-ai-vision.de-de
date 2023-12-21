---
title: Azure KI Vision-Übungen
permalink: index.html
layout: home
---

# Azure KI Vision-Übungen

Die folgenden Übungen sind eine Ergänzung für die Module in Microsoft Learn.


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
