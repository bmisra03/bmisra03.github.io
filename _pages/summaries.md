---
layout: page
permalink: /summaries/
title: ML Paper Summaries
description: >
nav: true
nav_order: 2
---

Summaries of papers I've read and find interesting. Covers the key points, criticisms, and my own implementation/analysis of the code (if applicable). Heavily inspired by [Fan Pu Zeng's](https://fanpu.io/) blog. NOTE: these summaries contain information that I find most interesting, and will not necessarily go into detail about all points covered in their publications. 

This page serves as a way to document my academic progress, as well as showcase my technical interests and analytical skills to anyone who may be interested. Most posts will pertain to the following topics: 

__Deep Learning in Computer Vision, Federated Learning, ML Privacy and Security__

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