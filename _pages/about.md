---
permalink: /
title: "About me"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

This is my attempt at a personal blog. Hello World!

I am currently pursuing Masters in Computer Science at the Courant Institute of Mathematical Sciences, NYU. Before that, I was a Data Scientist at UnitedHealth Group, a Fortune 5 Pharmacy Benefits Manager.

Looking to get internship opportunities in the field of natural language processing and application. Interested in smart assistants, language understanding, and sequential problems.

Thoughts and Qs from my projects
=====
{% for post in site.portfolio %}
  {% include archive-single.html type="grid" %}
{% endfor %}
