---
title: Introduction
sitemap:
    lastmod: '22-08-2024 13:32'
partials:
    header_subtitle:
        toggle: true
    metadata:
        where: header
    breadcrumbs:
        toggle: false
feed:
    limit: 10
show_sidebar: false
process:
    markdown: true
    twig: false
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

<table border="1" class="docutils">
<thead>
<tr>
<th>version</th>
<th>date</th>
<th>faits notables</th>
</tr>
</thead>
<tbody>
<tr>
<td>1.0</td>
<td>janvier 1996</td>
<td>La naissance</td>
</tr>
<tr>
<td>1.1</td>
<td>février 1997</td>
<td>Ajout de JDBC et définition des JavaBeans</td>
</tr>
<tr>
<td>1.2</td>
<td>décembre 1998</td>
<td>Ajout de Swing, des collections (JCF), La machine virtuelle inclut la  compilation à la volée (Just In Time)</td>
</tr>
<tr>
<td>1.3</td>
<td>mai 2000</td>
<td>JVM HotSpot</td>
</tr>
<tr>
<td>1.4</td>
<td>février 2002</td>
<td>support des regexp et premier parser de XML</td>
</tr>
<tr>
<td>5</td>
<td>septembre 2004</td>
<td>évolutions majeures du langage : autoboxing, énumérations, varargs, imports statiques, foreach, types, génériques, annotations. Nombreux ajout dans l\\'API standard</td>
</tr>
<tr>
<td>6</td>
<td>décembre 2006</td>
<td></td>
</tr>
<tr>
<td>7</td>
<td>juillet 2011</td>
<td>Quelques évolutions du langage et l\\'introduction de java.nio, try-with-resources</td>
</tr>
<tr>
<td>8 LTS</td>
<td>mars 2014</td>
<td>évolutions majeures du langage : les lambdas et les streams et une nouvelle API pour les dates,</td>
</tr>
<tr>
<td>9</td>
<td>septembre 2017</td>
<td>les modules (projet Jigsaw) et jshell</td>
</tr>
<tr>
<td>10</td>
<td>mars 2018</td>
<td>inférence des types pour les variables locales (mot-clé <code>var</code>)</td>
</tr>
<tr>
<td>11 LTS</td>
<td>Sept 2018</td>
<td>classes Strings et Files, execution direct de fichiers Java</td>
</tr>
<tr>
<td>12</td>
<td>Mars 2019</td>
<td>Unicode 11</td>
</tr>
<tr>
<td>13</td>
<td>September 2019</td>
<td>Unicode 12.1, Switch Expression, Multiline Strings</td>
</tr>
<tr>
<td>14</td>
<td>March 2020</td>
<td>Records,  Pattern Matching pour InstanceOf</td>
</tr>
<tr>
<td>15</td>
<td>September 2020</td>
<td>String multilignes, classes célées</td>
</tr>
<tr>
<td>16</td>
<td>March 2021</td>
<td></td>
</tr>
<tr>
<td>17 LTS</td>
<td>September 2021</td>
<td>Objets dans les switches</td>
</tr>
<tr>
<td>18</td>
<td>March 2022</td>
<td>API vecteurs</td>
</tr>
<tr>
<td>19</td>
<td>September 2022</td>
<td></td>
</tr>
<tr>
<td>20</td>
<td>March 2023</td>
<td></td>
</tr>
<tr>
<td>21 LTS</td>
<td>September 2023</td>
<td></td>
</tr>
</tbody>
</table>
