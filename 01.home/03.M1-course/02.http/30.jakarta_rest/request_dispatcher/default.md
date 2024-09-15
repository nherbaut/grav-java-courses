# RequestDispatcher et MVC

Le [request dispatcher](https://docs.oracle.com/javaee/7/api/javax/servlet/RequestDispatcher.html)
est un objet fourni par le conteneur Web. Il permet d’inclure ou de
déléguer des traitements lors de la prise en charge d’une requête HTTP.
Nous allons voir dans un premier temps comment cet objet peut être
utilisé. Dans un second temps, nous verrons ce que l’utilisation d’un
request dispatcher apporte dans la conception d’architecture Web en
prenant comme exemple le modèle MVC.

## Le request dispatcher

Nous avons vu précédemment qu’il existe une classe [ServletContext](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html)
permettant notamment de stocker les attributs de portée application. Le
[ServletContext](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html) représente le contexte d’une application Web et est
accessible depuis une servlet grâce à la méthode [getServletContext()](https://docs.oracle.com/javaee/7/api/javax/servlet/GenericServlet.html#getServletContext--).

Une instance de [ServletContext](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html) permet également de récupérer une
instance de [RequestDispatcher](https://docs.oracle.com/javaee/7/api/javax/servlet/RequestDispatcher.html) grâce aux méthodes :

[getResquestDispatcher(String path)](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html#getRequestDispatcher-java.lang.String-)
: Permet de récupérer une instance de RequestDispatcher pour
  transferer le traitement à la ressource dont le chemin d’URL est
  passé en paramètre
  <br/>
  ```java
  RequestDispatcher dispatcher = getServletContext().getRequestDispatcher("/un/chemin");
  ```
  <br/>
  Selon la documentation, le chemin passé en paramètre **DOIT**
  commencer par /. Cependant, le chemin est interprété relativement au
  contexte racine de l’application.

[getNamedDispatcher(String name)](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContext.html#getNamedDispatcher-java.lang.String-)
: Permet de récupérer une instance de RequestDispatcher pour
  transférer le traitement à la servlet dont le nom est passé en
  paramètre
  <br/>
  ```java
  RequestDispatcher dispatcher = getServletContext().getNamedDispatcher("maServlet");
  ```
  <br/>
  Une servlet peut être nommée dans le fichier web.xml grâce à la
  balise <servlet-name> ou avec l’annotation [@WebServlet](https://docs.oracle.com/javaee/7/api/javax/servlet/annotation/WebServlet.html)
  grâce à l’attribut name de l’annotation.

Une fois que nous disposons d’une instance d’un RequestDispatcher nous
pouvons appeler une de ses deux méthodes disponibles :

[void RequestDispatcher.include(ServletRequest request, ServletResponse response)](https://docs.oracle.com/javaee/7/api/javax/servlet/RequestDispatcher.html#include-javax.servlet.ServletRequest-javax.servlet.ServletResponse-)
: Inclut le contenu de la ressource dans le résultat final renvoyé au
  client. Si le request dispatcher pointe sur un fichier HTML,
  l’ensemble du fichier sera inséré dans la réponse. Si le request
  dispatcher pointe sur une servlet, cette dernière est exécutée et sa
  sortie est insérée dans la réponse.

[void RequestDispatcher.forward(ServletRequest request, ServletResponse response)](https://docs.oracle.com/javaee/7/api/javax/servlet/RequestDispatcher.html#forward-javax.servlet.ServletRequest-javax.servlet.ServletResponse-)
: Délègue le traitement de la requête à une nouvelle ressource. La
  différence avec la méthode `include` est que la servlet qui
  appelle `forward` ne doit pas avoir produit de contenu dans la
  réponse.

Avec le request dispatcher, on voit apparaître la possibilité de créer
une chaîne de traitement pour une requête. Par exemple, une servlet peut
être utilisée pour valider les paramètres transmis par la requête et
effectuer un traitement propre à l’application. Puis, *via* un
RequestDispatcher, elle peut déléguer à une autre servlet le soin de
générer la réponse en utilisant la méthode `forward`. C’est dans ce
modèle de traitement que les attributs de requête vus dans le chapitre
[Les attributs d’une application Web](attributs_web.md#attributs-web) vont être utiles. Chaque
servlet impliquée dans le traitement de la requête exploite et produit
des données qu’elle peut récupérer ou stocker comme attribut dans la
requête.

## Le modèle MVC

L’utilisation d’un [RequestDispatcher](https://docs.oracle.com/javaee/7/api/javax/servlet/RequestDispatcher.html) pour segmenter le traitement d’une
requête HTTP a permis très tôt aux développeurs d’application Web en
Java d’imaginer un modèle d’architecture. Ce modèle est basé sur un
modèle de conception déjà utilisé pour le développement d’application
graphique : le [MVC](https://fr.wikipedia.org/wiki/Mod%C3%A8le-vue-contr%C3%B4leur) (modèle-vue-contrôleur).

Le MVC découpe le traitement applicatif selon trois catégories :

**Le modèle**
: Il contient les données applicatives ainsi que les logiques de
  traitement propres à l’application.

**La vue**
: Elle gère la représentation graphique des données et l’interface
  utilisateur

**Le contrôleur**
: Il est sollicité par les interactions de l’utilisateur ou les
  modifications des données. Il assure la cohérence entre le modèle et
  la vue.

Pour une application Web Java

- le modèle peut être un simple objet Java qui encapsule la logique de
  l’application
- la vue peut être une page HTML ou une Java Server Pages (JSP)
- le contrôleur est une servlet chargée de valider les paramètres de la
  requête avant de les transmettre au modèle pour le traitement. Une
  fois ce traitement terminé, la servlet transmet le résultat à la vue
  grâce au RequestDispatcher.

## Exercice
