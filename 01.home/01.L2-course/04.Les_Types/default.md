---
title: Les Types
---

# Les Types

Nous avons déjà vu de nombre type dans le exemples précédents.

```java
Point point=new Point();
int i=4;
```

Les types nous permettent de comprendre ce que représentent des données en mémoire, et donc de traduire en valeur ou en adresse une série de bits.
Java étant un langage de programmation utilisant un typage fort, il permet de savoir, lors de la compilation l'ensemble des opérations réalisables sur une variable. Par exemple:

<iframe src="https://java.miage.dev?gistId=560867a7e6d72cc4c8340cdbc509c228" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>


## Types de conversion

* certaines conversions sont implicites: 
le compilateur ne considère que les types, et pas les valeurs avant de faire les conversions

```java
int x=3;
double d=x; //conversion implicite
```

* Certaines conversions sont explicites:
  * Le compilateur a besoin d'être sûr que nous sommes ok avec la perte de précision!

```java
double d=3.14;
int x = (int) d; //perte de précision!!
```

* certaines conversions nécéssite l’utilisation de fonctions spécialisées

```java
String s="3";
int x = Integer.parseInt(s);
```

## types primitifs vs Objets

* Il exite 8 types primitifs en java : *int, long, float, double, char,  boolean, byte, short*
  * stockent directement leur valeur
  * ne peuvent être *null*
  * on ne peut pas invoquer de méthodes
  * Il existe des classes enveloppe équivalentes (Intger, Long etc.)

* Objets: Shape, Point, String...
  * ils ne stockent pas des valeurs, mais des références vers des valeurs  
  * On peut accéder aux attributs à l'aide de l'opérateur **.**
  * Peuvent être **null**
  * on peut utiliser **==** pour savoir si deux références sont égales.

> Arrivez vous à voir la différence dans le nommage de types entre les primitifs et les objets?


# Les boucles for

## Version ForEarch

<iframe src="https://java.miage.dev?gistId=6c1663c3cbf2f6e9469529980d182ccb" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

## Version Classique

<iframe src="https://java.miage.dev?gistId=4d8a3fb1d8edcbe5a1113df248f85d85" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

# Exercices Classes, types et boucles for


## Exo 1

<iframe src="https://java.miage.dev?gistId=0b724db417733768b62c5df3396413e0" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

## Exo 2

<iframe src="https://java.miage.dev?gistId=fdb9ddd5d9358d028804d70156cba760" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

## Exo 3

<iframe src="https://java.miage.dev?gistId=e9c49bc4597e4cb087ed48130475943d" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>


## Exo 4 

<iframe src="https://java.miage.dev?gistId=91f68c0e79f4b6c5cfede0b43006040f" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

## Exo 5

<iframe src="https://java.miage.dev?gistId=7d2e671c85856dd90ae2a5e3e59fbfdd" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>



