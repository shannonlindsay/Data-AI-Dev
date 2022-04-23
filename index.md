---
title: Online Hosted Instructions
permalink: index.html
layout: home
---
# Instructions

Work with your partner to split up and test the labs in any way you see fit. What is important is that between the two of you, all 13 labs are tested. **Please complete all testing by 4pm PST on Friday, April 29**. This deadline is important as the lab environments are only live until this date/time.

Together with your partner, test each of the labs below and provide feedback via a GitHub **[issue](https://github.com/shannonlindsay/Data-AI-Dev/issues/new)** or **[pull request](https://github.com/shannonlindsay/Data-AI-Dev/pulls)**. If you are less familiar with GitHub, log an **issue**, where you'll describe the issue you are seeing in the lab.

Please look at the [other issues](https://github.com/shannonlindsay/Data-AI-Dev/issues) before you log a new issue. If someone has already reported it, you can comment on their issue to elaborate or add context.

# Content Directory

Hyperlinks to each of the lab exercises and lab environments are listed below. Log in to the lab environment with your Microsoft credentials. Please let me know ASAP in the Teams chat if you're having any issues with access. For this week's testing, lab instructions are available in the instructions pane on the VM and via GitHub using the hyperlinks below.

>**Note**: Logging in to the VM requires dual factor authentication. Have your device nearby!

For labs that require Azure services (Azure SQL DB or Azure Synapse Analytics), login/subscription information can be found on the resources tab in the VM. 

![Resources tab of VM containing subscription information. Pane on right side of screen with blue header.](https://user-images.githubusercontent.com/77289548/164934166-41296702-01b7-484b-ac23-1c9a23e7f1da.png)

Allow ~5 minutes for the **Improve performance with hybrid tables** lab to start. Also be aware that for the **Create a star schema model** and **Create a dataflow** labs, you need to run a script to load data that will take ~20 minutes.

There are three lab environments for this course. The links below will take you to the correct environment, but it may be useful to know when splitting up the labs between you and your partner.

1.	Labs that require Power BI only (10 labs, [lab profile 117782](https://labondemand.com/AuthenticatedLaunch/117782?providerId=1))
    - Create a composite model
    - Create a paginated report
    - Create calculation groups
    - Create reusable Power BI artifacts
    - Enforce model security
    - Improve query performance with aggregations
    - Improve query performance with dual storage mode
    - Monitor data in real-time with Power BI
    - Use tools to optimize Power BI performance
    - Work with model relationships
2.	Labs that also require Azure Synapse Analytics (2 labs, [lab profile 111889](https://labondemand.com/AuthenticatedLaunch/111889?providerId=1))
    - Create a star schema model
    - Create a dataflow
3.	Labs that also require Azure SQL Database (1 lab, [lab profile 119734](https://labondemand.com/AuthenticatedLaunch/119734?providerId=1))
    - Improve query performance with hybrid tables

## Labs

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
| Module | Lab | Lab Profile|
| --- | --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) | [Launch Lab]({{activity.lab.labprofile}}){:target="_blank"} |
{% endfor %}
