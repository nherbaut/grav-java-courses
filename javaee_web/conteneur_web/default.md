# Le conteneur Web

Nous avons vu au chapitre précédent que les servlets sont des composants
Web qui permettent de répondre à des requêtes utilisateurs. Ces servlets
sont packagées dans une application Web qui est elle-même déployée dans
un serveur d’application Java EE. Cependant, nous n’avons pas eu à
écrire de lignes de code telles que :

```java
MaServlet servlet = new MaServlet();
```

C’est-à-dire que nous n’avons pas eu à instancier nos servlets et
pourtant, elles ont bien été créées et utilisées pour générer les
réponses dynamiques.

Un serveur Java EE fournit un **conteneur Web** (parfois appelé
conteneur de servlets). Un conteneur a la charge d’instancier,
d’initialiser et de détruire les servlets d’une application. C’est
également le conteneur qui fournit une instance de HttpServletRequest et
de HttpServletResponse pour chaque requête.

Nous allons voir en détail la gestion du cycle de vie des servlets par
le conteneur Web et les conséquences que cela a sur la façon de
développer une application Web.

## Cycle de vie des servlets

Le conteneur Web gère le cycle de vie des servlets : la création,
l’initialisation et la destruction. À chacune de ces étapes, une
instance de servlet est informée par un appel à une méthode déclarée
dans l’interface [Servlet](https://docs.oracle.com/javaee/7/api/javax/servlet/Servlet.html) et qui peut être redéfinie pour chaque servlet.

```java
public void init(ServletConfig config) throws ServletException {
  // appelée au moment de l'initialisation de la servlet
}

public void destroy() {
  // appelée avant la suppression de la servlet du conteneur
}
```

De plus la servlet sera prévenue de sa création par un appel à son
constructeur. Cela a une conséquence importante pour l’implémentation
d’une servlet : **une servlet doit obligatoirement avoir un constructeur
sans paramètre**.

#### NOTE
En Java, une classe qui ne déclare pas de constructeur dispose néanmoins
d’un constructeur par défaut. Il s’agit d’un constructeur sans
paramètre. Ainsi :

```java
public class MaClasse {

}
```

est équivalent à :

```java
public class MaClasse {
    public MaClasse() {
        super();
    }
}
```

Lors de l’appel à la méthode `init(ServletConfig)`, le conteneur passe
en paramètre une instance de [ServletConfig](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletConfig.html)
qui permet, entre-autres, à la servlet de récupérer des paramètres
d’initialisation. Notez que la méthode `init(ServletConfig)` autorise
l’implémentation à jeter une `ServletException`. Si cela se produit,
le conteneur considère que la servlet n’a pas pu s’initialiser
correctement et elle ne sera pas déployée dans le conteneur : elle ne
sera donc pas accessible !

Dans une servlet, il est préférable de redéfinir la méthode :

```java
public void init() throws ServletException {
}
```

Cette méthode est définie dans la classe parente de [HttpServlet](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html) et
si on désire accéder à l’objet `ServletConfig`, il est possible de le
faire avec la méthode `getServletConfig()`.

## Exercice

## Servlet et programmation concurrente

Avec l’exercice précédent, nous avons mis en lumière le fait que le
conteneur Web ne crée qu’une seule instance de chaque servlet. En fait,
le conteneur est libre d’adopter la stratégie qui lui paraît la
meilleure. Nous avons également constater que nous pouvons changer le
moment où le conteneur instanciera une servlet grâce à l’attribut
`loadOnStartup` (cette option est disponible également dans le fichier
de déploiement web.xml avec la balise `<load-on-startup>`).

En tant que développeur de servlet, il faut donc **toujours** garder à
l’esprit qu’une même instance de servlet sera utilisée pour servir
plusieurs requêtes HTTP, y compris des requêtes simultanées. Cela
introduit dans le développement de servlet, le problème de la
programmation concurrente. Tout changement de l’état interne d’une
servlet peut entraîner un bug potentiel pour des requêtes qui sont
traitées en parallèle.

Pour éviter tout problème, il faut s’assurer que les modifications des
attributs d’une servlet sont sûres dans un contexte d’exécution
concurrent. Il faut également s’assurer que les objets manipulés par la
servlet et qui ne sont pas explicitement créés pour une requête, peuvent
être utilisés dans un environnement concurrent (**thread-safe**).
Nous verrons que la problèmatique de programmation concurrente est
récurrente dans le développement d’application Java EE.

## Exercice
