---
layout: page
permalink: /repositories/
title: Repos
description: Small-scale projects, code samples, and scripts
nav: true
nav_order: 3
---

{% if site.data.repositories.github_repos %}

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% for repo in site.data.repositories.github_repos %}
    {% include repository/repo.liquid repository=repo %}
  {% endfor %}
</div>
{% endif %}
