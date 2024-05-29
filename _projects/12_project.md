---
layout: page
title: This Website
description: Portfolio website created with al-folio and Jekyll
img: assets/img/project_12/portfolio_site_screenshot.png
importance: 8
category: websites
---

Experimenting with Google Sites and Wix before finding <a href="https://github.com/alshedivat/al-folio">al-folio</a>, I chose this <a href="https://jekyllrb.com/">Jekyll</a> theme for its minimalist back-end that would allow me the customization I wanted to set my site apart from others. At the forefront of this site's design, I wanted viewers to navigate effectively while also engaging with the content provided, rather than scrolling through just another portfolio site.

As I worked on retheming this site, I encountered some obstacles to customization that I noticed had gone severly undocumented on the al-folio forums. If you're interested, see below for code examples of the following:

<br>

<h3>Adding and Changing Social Icons</h3>

<br>

<i>_includes/head.liquid</i>

Go to <a href="https://fontawesome.com/">fontawesome</a>, create a profile, and get your free web fonts kit.
Then add your Kit Embed code under the ```<!-- Fonts & Icons -->``` section. It should look like below.
```
<script src="https://kit.fontawesome.com/##########.js" crossorigin="anonymous"></script>
```

<br>

<i>_includes/social.liquid</i>

Now go to your desired social link (Ctrl + F to find the link) and change the following to whatever icon you want:
```
<i class="fa-CATEGORY fa-ICON">
```

<br>

For example, in _config.yml I set the 'work' social link to my itch.io page and then changed
```
<a href="{{ site.work_url }}" title="Work"><i class="fa-solid fa-briefcase"></i></a>
```
to
```
<a href="{{ site.work_url }}" title="Work"><i class="fa-brands fa-itch-io"></i></a>
```

<br>
<br>

<h3>Hosting Interactive PDFs on Project Pages</h3>
To link a scrollable PDF on your project page, add the PDF to 'assets/pdf' and add the following to the project .md file.
```
<object data="{{ site.url }}{{ site.baseurl }}/assets/pdf/FILENAME.pdf" width="800" height="500" type='application/pdf'></object>
```

<br>
<br>

<h3>Adding a Papers section</h3>
Go to '_pages', copy ```projects.md```, and rename it to ```papers.md```.
Then set the header to the following
```
---
layout: page
title: Papers
permalink: /papers/
description: DESCRIPTION
nav: true
nav_order: #
display_categories: [DIFFERENT CATEGORIES THAN PROJECTS]
horizontal: false
---
```
When creating a new project now, make sure to set the category to one set above.