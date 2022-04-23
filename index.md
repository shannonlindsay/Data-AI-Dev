---
title: Online Hosted Instructions
permalink: index.html
layout: home
---
# Instructions

Together with your partner, test each of the labs below and provide feedback via a GitHub **[issue](https://github.com/shannonlindsay/Data-AI-Dev/issues/new)** or **[pull request](https://github.com/shannonlindsay/Data-AI-Dev/pulls)**. If you are less familiar with GitHub, log an **issue**, where you'll describe the issue you are seeing in the lab.

Please look at the [other issues](https://github.com/shannonlindsay/Data-AI-Dev/issues) before you log a new issue. If someone has already reported it, you can comment on their issue.

# Content Directory

Hyperlinks to each of the lab exercises and demos are listed below.

## Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| Module | Lab | Lab Profile|
| --- | --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | {{activity.lab.labprofile}} [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
