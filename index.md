---
title: Azure AI 文档智能练习
permalink: index.html
layout: home
---

# Azure AI 文档智能练习

以下练习旨在支持 Microsoft Learn 上的模块。


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
