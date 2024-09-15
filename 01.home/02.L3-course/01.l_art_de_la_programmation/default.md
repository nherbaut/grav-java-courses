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
title: 'Le logiciel mange le monde'
media_order: tiobe.png
---

# Le logiciel mange le monde

Aujourd’hui, le logiciel est présent dans toutes les sphères de nos
vies. L’exemple le plus frappant est l’influence des réseaux sociaux. Ils
représentent désormais la porte d’entrée principale de l’information.
Dans ce cadre, ils opèrent une hiérarchisation de chacun de nos fils
d’actualité en fonction de nos préférences et influence donc grandement
pour le meilleur ou pour le pire notre vision du monde. Ces processus
éditoriaux sont entièrement automatisés par des algorithmes, écrits dans
des langages de programmation permettant d’exécuter concrètement les
outils imaginés par les statisticiens, les mathématiciens, les experts
du langage naturel, psychologues…

L’impact sociétal du logiciel se diffuse dans toute la société sans même
que les utilisateurs puissent comprendre la façon dont fonctionne un
programme ni même comment pensent ceux qui le réalise.

L’objectif de ce cours est de vous apprendre à penser comme des
informaticiens ou des *computer scientists* (le terme anglais *computer
scientists* rend un meilleur hommage à la discipline en mettant en avant
son aspect scientifique).

L’approche scientifique de l’informatique combine plusieurs domaines,
comme les mathématiques, l’ingénierie et les sciences naturelles:

1. Comme les mathématiciens, les informaticiens utilisent des langages
   formels pour exprimer des idées, en l’occurrence des calculs
2. Comme les ingénieurs, ils créent et assemblent des composants dans
   des systèmes et arbitrent entre les différentes possibilité
   d’implémentation
3. Comme les scientifiques, ils observent les comportements de systèmes
   complexes, forment des hypothèses et testent leurs prédictions.

La qualité principale d’un informaticien est d’avoir un esprit *orienté
vers la résolution des problèmes* (problem solving). De formuler les
problèmes, de penser de façon créative et exprimer des solutions
clairement. Pour cela, une bonne maîtrise du langage de programmation
est indispensable.

**Votre objectif dans ce cours est d’apprendre comment utiliser le
langage JAVA pour résoudre des problèmes et d’apprendre comment ses
spécificités peuvent vous aider à produire du code rapide, sans bug, et
qui exécute les opérations que vous avez décidé**

# La programmation Orientée Objet et le JAVA

## Pourquoi le JAVA
![tiobe](tiobe.png "tiobe")

https://www.tiobe.com/tiobe-index

Java est le langage le plus utilisé dans l’informatique de gestion, mais
peut être utilisé dans de nombreux autres cas. Dans son écosystème, on
trouve de très bons outils très robustes et rapides. Ce langage vous
permet de très bien “vendre” votre CV, pour trouver des stages et des
emplois après une formation spécialisée en informatique.

## Compréhension intuitive dans la programmation orientée objet

La programmation Orientée objet et le *paradigme* de programmation le
plus populaire au monde actuellement. Ses avantages sont qu’il permet de
modéliser le système d’information à l’aide de concepts naturels à
l’être humain, qui découpent instinctivement le monde en classes (un.e
étudiant.e, un.e prof, un.e administrateur.rice ). Chaque individus
appartenant à une classe possède des attributs qui lui sont propre
(chaque prof a un nom, chaque étudiant.e a un numéro INE) et réalise des
actions communes à tous les individus de sa classe (chaque prof
enseigne, chaque étudiant apprend).

Les classes d’individus peuvent être réparties en famille, dès lors
qu’une classe peut en spécialiser une autre. Par exemple, les prof et
les étudiants.es sont des humains et *héritent* donc de certaines
propriété des individus de la classe des humains. Si on dit que tous les
être humains possèdent un nom et que les prof sont des être humains,
alors un prof possède un nom.