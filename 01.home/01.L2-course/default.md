---
title: 'L2 MIASHS POO Introduction'
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

Introduction à la programmation orientée objet avec Java

## Prérequis

* notions de programmation python
* accès administrateur sur votre poste de travail

## Contenu de l’enseignement


## Méthodes pédagogiques

Le cours est décomposé en 10 séances « cours » de 1h centrées sur des notions, accompagnées de 10 séances de TD centrées sur la mise en pratique des notions, ainsi que 2 séances « projet » de 3h centrées sur un projet de programmation avec un accompagnement individuel. 

Chaque séance « cours »contient la présentation des notions, un contrôle des connaissances et une mise en pratique par la programmation. Les séances « projet » permettent la mise en œuvre de l’ensemble des compétences acquises pendant le cours.

# Contenu
<ol>
{% for p in page.collection %}
<li><a href="{{ p.url }}">{{ p.title|e }}</a></li>
{% endfor %}
</ol>