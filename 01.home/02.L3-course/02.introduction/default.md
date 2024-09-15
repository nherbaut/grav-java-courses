---
feed:
    limit: 10
partials:
    breadcrumbs:
        toggle: false
    header_subtitle:
        toggle: true
    metadata:
        where: header
process:
    markdown: true
    twig: false
show_sidebar: false
sitemap:
    lastmod: '22-08-2024 13:32'
title: Introduction
---

# Introduction

Java est un langage de programmation originellement proposé par Sun
Microsystems et maintenant par Oracle depuis son rachat de Sun
Microsystems en 2010.

Java a été conçu avec deux objectifs principaux :

* Permettre aux développeurs d’écrire des logiciels indépendants de
  l’environnement *hardware* d’exécution.
* Offrir un langage orienté objet avec une bibliothèque standard riche

## L’environnement

L’indépendance par rapport à l’environnement d’exécution est garantie
par la *machine virtuelle Java* (Java Virtual Machine ou **JVM**). En
effet, Java est un langage compilé mais le compilateur ne produit pas de
code natif pour la machine, il produit du
[bytecode](https://fr.wikipedia.org/wiki/Bytecode_Java) : un jeu
d’instructions compréhensibles par la JVM qu’elle va traduire en code
exécutable par la machine au moment de l’exécution.

Pour qu’un programme Java fonctionne, il faut non seulement que les
développeurs aient compilé le code source mais il faut également qu’un
environnement d’exécution (comprenant la JVM) soit installé sur la
machine cible.

Il existe ainsi deux environnements Java qui peuvent être téléchargés et
installés depuis le [site
d’Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
:

JRE - Java Runtime Environment
: Cet environnement fournit uniquement les outils nécessaires à
  l’exécution de programmes Java. Il fournit entre-autres la machine
  virtuelle Java.

JDK - Java Development Kit
: Cet environnement fournit tous les outils nécessaires à l’exécution
  mais aussi au développement de programmes Java. Il fournit
  entre-autres la machine virtuelle Java et la compilateur.

## JAVA JDK

Depuis 2006, le code source Java (et notamment le code source de la JVM)
est progressivement passé sous licence libre GNU
[GPL](https://fr.wikipedia.org/wiki/Licence_publique_g%C3%A9n%C3%A9rale_GNU)
avec quelques exceptions concernant des modules propriétaires (Java
Plugin, Java WebStart, Swing Rasterizer, polices de caractères).
Celui-ci est maintenu exclusivement par Oracle dans le cadre du projet
[Open JDK](http://openjdk.java.net/). D’autres JVM, la plus part
dérivées d’Open JDK, existent. Tout en restant compatibles avec le
standard, elles comportent certaines différences (meilleures
performances pour certains scenarii , support d’architecture
exotiques.…)

## Un bref historique des versions

| version | date           | faits notables                                                                                                                                                       |
|---------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.0     | janvier 1996   | La naissance                                                                                                                                                         |
| 1.1     | février 1997   | Ajout de JDBC et définition des JavaBeans                                                                                                                            |
| 1.2     | décembre 1998  | Ajout de Swing, des collections (JCF), La machine virtuelle inclut la  compilation à la volée (Just In Time)                                                         |
| 1.3     | mai 2000       | JVM HotSpot                                                                                                                                                          |
| 1.4     | février 2002   | support des regexp et premier parser de XML                                                                                                                          |
| 5       | septembre 2004 | évolutions majeures du langage : autoboxing, énumérations, varargs, imports statiques, foreach, types, génériques, annotations. Nombreux ajout dans l\\'API standard |
| 6       | décembre 2006  |                                                                                                                                                                      |
| 7       | juillet 2011   | Quelques évolutions du langage et l\\'introduction de java.nio, try-with-resources                                                                                   |
| 8 LTS   | mars 2014      | évolutions majeures du langage : les lambdas et les streams et une nouvelle API pour les dates,                                                                      |
| 9       | septembre 2017 | les modules (projet Jigsaw) et jshell                                                                                                                                |
| 10      | mars 2018      | inférence des types pour les variables locales (mot-clé var)                                                                                                         |
| 11 LTS  | Sept 2018      | classes Strings et Files, execution direct de fichiers Java                                                                                                          |
| 12      | Mars 2019      | Unicode 11                                                                                                                                                           |
| 13      | September 2019 | Unicode 12.1, Switch Expression, Multiline Strings                                                                                                                   |
| 14      | March 2020     | Records,  Pattern Matching pour InstanceOf                                                                                                                           |
| 15      | September 2020 | String multilignes, classes célées                                                                                                                                   |
| 16      | March 2021     |                                                                                                                                                                      |
| 17 LTS  | September 2021 | Objets dans les switches                                                                                                                                             |
| 18      | March 2022     | API vecteurs                                                                                                                                                         |
| 19      | September 2022 | certaines API sont passées en Preview et améliorées                                                                                                                  |
| 20      | March 2023     | certaines API sont passées en Preview et améliorées                                                                                                                  |
| 21 LTS  | September 2023 | pattern matching pour les switch, thread virtuels, String templates, sealed class                                                                                    |
| 22      | Mars 2024      | variables anonymes, Foreign Function & Memory API pour le code natif, instructions avant le super dans les constructeurs                                             |
| 23      | Septembre 2024 | Types primitis dans les patterns, manipulation des fichiers class native                                                                                             |
| 24      | Mars 2025      | |
| 25 LTS      | Septembre 2025 | |
