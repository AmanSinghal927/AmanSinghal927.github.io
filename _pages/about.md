---
permalink: /
title: "About me"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

I am a skilled data scientist with extensive experience in computer vision, natural language processing, and machine learning techniques. I hold a Master of Science in Computer Science from New York University, where I am currently enrolled, and a Bachelor of Technology in Electronics and Communications Engineering from Delhi Technological University.

I have worked as a Data Scientist at UnitedHealth Group, where I implemented computer vision and natural language processing tasks, won a global hackathon, and led several successful projects in legal text generation, named entity recognition, and text classification. I also worked as a Research Assistant at CVIT Lab, IIIT Hyderabad, where I explored pairwise NMT for Indian languages and achieved leaderboard across 10 language modeling tasks at the Workshop for Asian Translation (WAT’20).

I have strong skills in Python, SQL, R, C++, JavaScript, and many other programming languages. I am proficient in various software tools such as Jupyter, Apache, and PySpark and have hands-on experience with libraries such as Scikit-Learn, Keras, PyTorch, and TensorFlow.

I have deployed production systems using Kubernetes, Docker, and Git and have experience with cloud platforms like AWS EC2 and S3. I also have strong skills in data analysis, data visualization, and statistical modeling, using tools such as Tableau, Matplotlib, and Statsmodel.

My research projects include a semantic search engine for contract intelligence, audio analytics for content moderation, insurance risk predictive modeling, and low-cost human feedback. I have also published papers at various international conferences and journals.

I am an enthusiastic team player with excellent communication skills and a passion for data-driven problem-solving. I am always looking for new challenges and opportunities to learn and grow as a data scientist.

<!-- Thoughts and Qs from my projects -->
<!-- ===== -->
{% for post in site.portfolio %}
  {% include archive-single.html type="grid" %}
{% endfor %}
