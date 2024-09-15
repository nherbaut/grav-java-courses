---
title: 'Messaging'
show_sidebar: true
show_breadcrumbs: true
show_pagination: true
content:
    items: '@self.children'
    order:
        by: default
        dir: asc
sitemap:
    lastmod: '22-08-2024 16:34'
twig_first: true
process:
    markdown: true
    twig: true
---

# Objectif de l’enseignement

Utilisation du messaging en Jakarta EE, à l'aide de Camel

# Contenu
<ol>
{% for p in page.collection %}
<li><a href="{{ p.url }}">{{ p.title|e }}</a></li>
{% endfor %}
</ol>
