---
title: 'Communication HTTP'
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

Utilisation du protocole HTTP pour la mise à disposition d'API sur la stack Internet.

# Contenu
<ol>
{% for p in page.collection %}
<li><a href="{{ p.url }}">{{ p.title|e }}</a></li>
{% endfor %}
</ol>
