---
title: Online Hosted Instructions
permalink: index.html
layout: home
---
# Instructions

Together with your partner, test each of the labs below and provide feedback via a GitHub **[issue](https://github.com/shannonlindsay/Data-AI-Dev/issues/new)** or **[pull request](https://github.com/shannonlindsay/Data-AI-Dev/pulls)**. If you are less familiar with GitHub, log an **issue**, where you'll describe the issue you are seeing in the lab.

Please look at the [other issues](https://github.com/shannonlindsay/Data-AI-Dev/issues) before you log a new issue. If someone has already reported it, you can comment on their issue to elaborate or add context.

# Content Directory

Hyperlinks to each of the lab exercises and lab environments are listed below. Log in to the lab environment with your Microsoft credentials. Please let me know ASAP in the Teams chat if you're having any issues with access.

>**Note: 
>Logging in to the VM requires dual factor authentication. Have your device nearby!

For this week's testing, lab instructions will **not** be in the VM. Access the lab instructions here in GitHub. For labs that require Azure services (Azure SQL DB or Azure Synapse Analytics), login/subscription information can be found on the resources tab in the VM. The labs that require Azure services take longer to start, as there are scripts loading resources in the background. Allow ~5 minutes for the **Improve performance with hybrid tables** lab to start and ~10 minutes for the **Create a star schema model** and **Create a dataflow** labs to start.

## Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| Module | Lab | Lab Profile|
| --- | --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) | [Launch Lab]({{activity.lab.labprofile}}){:target="_blank"} |
{% endfor %}
