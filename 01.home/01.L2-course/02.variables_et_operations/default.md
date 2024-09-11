---
title: 'Les Variables & Opérations mathématiques'
---

# Variables

## Déclaration et initialisation

Comme dit précédemment, les variables doivent toujours être déclarée.

```java
int x;
int y;
```

Lors de la déclaration de variables, si aucune initialisation n'est faite, une valeur par défaut est assignée automatiquement

```java
int x; //x vaudra 0
int y=5; //y vaudra 5 

```

## Les types de variables

Les variables peuvent être de différent types:

* *les types primitifs*: ce sont les types fondamentaux de tous langage de programmation. Leur liste est:
  * *boolean* : un variable booléenne peut être soit vrai soit faux (true or false)
  * *char*: un caractère
  * *byte*: un octet de 8 bits: `0x01010101`,  `0x00000001`, `0x10000000`
  * *short*: un entier de petite taille, compris dans \[-32768;32767\]
  * *int*: un entier de taille normale, compris dans \[-2147483648;2147483647\]
  * *long* un gros entier \[-9223372036854775808,9223372036854775807\]
  * *float*: un numbre à virgule normal dans \[-3.40E38;3.40E38\]
  * *double*: un gros numbre à virgule dans \[-1.8E308;1.8E308\]
* les types *Objets*: sont sont tous les autres

<iframe src="https://java.miage.dev/?snipId=573572bc-5025-41b5-ad6e-679be4315391" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>


## Utilisation des variables de type primitif

Les variables de types primitifs peuvent être utilisées pour réaliser des opérations arithmétiques.

### Exo 1

<iframe src="https://java.miage.dev?gistId=43a87ac01506876e53a4fe50a59088e4" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

### Exo 2

<iframe src="https://java.miage.dev?gistId=ce2992ecbd3311e48e30bce2131740b9" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>


# Les fonctions et les conditions

## Les fonctions

Les fonctions prennent entre 0 et n arguments et retournent 0 ou 1 valeur. La signature de la fonction est:

`type_retour nom_fonction(type_arg1 arg1, type_arg2 arg2, ....)`

pour appeller une fonction, il suffit de taper `nom_fonction(value1, value2)`

<iframe src="https://java.miage.dev/?snipId=72c5b606-949c-4e7a-9935-3448dba1f19e" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

## Les conditions

### Le IF 

Le If permet de choisir le déroulement de l'exécution des fonctions conditionnellement au résultat d'une opération:

la sytaxe est la suivante:

```java
if(condition1){
 // code exécuté si la condition est vraie
}
else if(condition2){
 //code exécuté si la condition 1 est fausse et que la condition 2 est vraie
}
else if(condition3){
 //code exécuté si les condition 1 et 2 sont fausse et que la condition 3 est vraie
}
else{
    //code exécuté si aucune  condition n'est vraie
}

```

#### Exo1 

<iframe src="https://java.miage.dev/?snipId=36ad186c-582c-4b67-8c2c-d4c9db545dbb" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

#### Exo 2

<iframe src="https://java.miage.dev/?snipId=9c35f598-e635-4c12-bb7c-ee3cc5d3f7c4" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

#### Exo 3

<iframe src="https://java.miage.dev/?snipId=c56f6100-8c97-4b6f-9afc-3ca79bc248dc" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>

#### Exo 4

<iframe src="https://java.miage.dev/?snipId=3171fdb3-d24e-43b4-a3d9-ceaa90076971" width="100%" height="800" frameborder="0" marginwidth="0" marginheight="0" allowfullscreen></iframe>


## Combiner des expressions dans des conditions

```java
boolean a=True;
boolean b=False;

System.out.println("a AND b:");
if( a && b ){
  System.out.println("false");
}
System.out.println("a OR b");
if( a || b){
  System.out.println("true");
}
System.out.println("NOT a OR NOT b");
if( !a || !b){
  System.out.println("true")
}
```

## Court-circuit de condition

* si on sait qu'une condition va être obligatoirement vrai en observant uniquement une partie, le calcul inutile sera court-cirtuité.

```java
String s1="Panthéon";
String s2="Sorbonne";

if ( s1.length()>6 && s1.charAt(6)=='o' ){
  System.out.println(s1);
}

if ( s2.length()>7 && s2.charAt(7)=='o' ){
  System.out.println(s2);
}
```