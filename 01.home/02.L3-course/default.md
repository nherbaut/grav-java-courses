---
content:
  items: '@self.children'
  order:
    by: default
    dir: asc
process:
  markdown: true
  twig: true
show_breadcrumbs: true
show_pagination: true
show_sidebar: true
sitemap:
  lastmod: 22-08-2024 16:34
title: L3 MIAGE INF2
twig_first: true
---
# Objectif de l’enseignement
Assurer l’autonomie des étudiants dans la pratique de la programmation orientée objet (POO) à l’aide du langage Java. Les étudiants seront capable de mettre en œuvres les principes d’une conception basée sur les objets et analyser un besoin pour le traduire en classes.

## Prérequis

* Une première expérience en programmation impérative ou des notions de POO.
* Analyse d’un besoin
* Utilisation d’un IDE

## Contenu de l’enseignement

* Structure du langage Java
* Classes et Objets
* Les méthodes statiques
* La class Object
* Les tableaux
* l'Héritage
* Les exceptions
* Polymorphisme
* Les classe Abstraites
* Les interfaces
* Les Collections
* Les entrées/sorties

## Méthodes pédagogiques

Le cours est décomposé en 4 séances « cours » de 2x3h centrées sur des notions et d’une séance « projet » de 2x3h centrée sur un projet de programmation. Chaque séance « cours »contient la présentation des notions, un contrôle des connaissances et une mise en pratique par la programmation. Les séances « projet » permettent la mise en œuvre de l’ensemble des compétences acquises pendant le cours.

# Contenu
<ol>
{% for p in page.collection %}
<li><a href="{{ p.url }}">{{ p.title|e }}</a></li>
{% endfor %}
</ol>