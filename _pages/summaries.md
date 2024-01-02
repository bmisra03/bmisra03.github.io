---
layout: page
permalink: /summaries/
title: ML Paper Summaries
description: >
nav: true
nav_order: 2
---

Summaries of papers I've read and find interesting. Covers the key points, criticisms, and my own implementation (if applicable). Heavily inspired by [Fan Pu Zeng](https://fanpu.io/).

{% include scripts/mathjax_macros.html %}

---

<ol>
    {% for summary in site.summaries reversed %}
    <li>
        <a href="{{ summary.url | relative_url }}">
            ({{ summary.date | date: '%b %-d, %Y' }})
            {{ summary.title }}
        </a>
    </li>
    {% endfor %}
</ol>