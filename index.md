---
title: Online Hosted Instructions
permalink: index.html
layout: home
---
# Instructions

Together with your partner, test each of the labs below and provide feedback via a GitHub **issue** or **pull request**. If you are unfamiliar with GitHub, log an **issue**, where you'll describe the issue you are seeing in the lab.

# Content Directory

Hyperlinks to each of the lab exercises and demos are listed below.

## Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| Module | Lab | 
| --- | --- |
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
