---
layout: vitae
title: CV
description: Curriculum Vitae
---

<!--
title: "Curriculum Vitae"
author: "Sara E. Moore"
output:
  html_document:
    toc: true
-->

<!--
### Contact Information

<ul class="fa-ul">
<li><i class='fa-li fa fa-envelope-square'></i><a href="mailto:{{ site.email }}">{{ site.email }}</a></li>
<li><i class='fa-li fa fa-external-link-square'></i><a href="{{ site.url }}">{{ site.url }}</a></li>
</ul>

-->

---


<div class="vitae vitae-default">

{% include education.html %}

{% include professional.html %}

{% include research.html %}

{% include teaching.html %}

{% include leadership.html %}

{% include volunteer.html %}

{% include awards.html %}

{% include skills.html %}

{% include publications.html %}

{% include references.html %}


</div>

Last updated {{ site.time | date_to_string }}

---

