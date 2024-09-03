# Java Server Faces (JSF)

Java Server Faces (JSF) est défini par la [JSR 344](https://jcp.org/en/jsr/detail?id=344). Il s’agit d’un
framework permettant de créer des applications Web complètes.

Il existe de nombreux frameworks permettant de développer des
applications Web en Java : JSF, Struts2, Spring MVC, Wicket, GWT, Play,
Tapestry… Lorsqu’on développe une application Web en Java, il peut
être difficile de savoir lequel choisir. Pour ce cours, nous nous
contenterons d’une introduction à JSF qui a la particularité d’être le
framework officiellement supporté pour Java EE et donc d’être intégré
dans le serveur d’application Wildfly.

## Créer un projet avec JSF

L’implémentation de JSF intégrée dans Wildfly est l’implémentation de référence
nommée *Mojarra*. *Mojarra* fournit une Servlet qu’il faut déclarer dans le
fichier `web.xml` de son application :

```xml
<servlet>
  <servlet-name>Faces Servlet</servlet-name>
  <servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
  <load-on-startup>1</load-on-startup>
</servlet>
<!--La servlet de JSF est configurée pour répondre à toutes les requêtes de fichiers XHTML-->
<servlet-mapping>
  <servlet-name>Faces Servlet</servlet-name>
  <url-pattern>*.xhtml</url-pattern>
</servlet-mapping>
```

JSF supporte une configuration de développement pour permettre un
rechargement presque à chaud lorsque des modifications sont apportées à
l’application. Pour l’activer, il faut rajouter **au début du fichier
web.xml** un paramètre d’application :

```xml
<context-param>
  <param-name>javax.faces.PROJECT_STAGE</param-name>
  <param-value>Development</param-value>
</context-param>
```

Comme tous les services Java EE, JSF dispose d’un fichier de déploiement
au format XML. Ce fichier de déploiement s’appelle `faces-config.xml`
et doit être situé dans le répertoire `WEB-INF`. Le contenu minimal de ce
fichier est :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<faces-config
  xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-facesconfig_2_2.xsd"
  version="2.2">

</faces-config>
```

Le fichier `faces-config.xml` est optionnel car il est possible d’intégrer
JSF en utilisant uniquement les annotations Java. Il est tout de même
recommandé d’ajouter ce fichier dans l’application même si son contenu
doit rester minimal.

Pour l’utilisation de JSF en vue d’un déploiement dans Wildfly, nous
allons également avoir besoin d’un service Java EE : **CDI** (Contexts
and Dependency Injection). Nous reviendrons plus tard sur l’utilité de
ce service. CDI n’est pas activé par défaut pour une application Web.
Pour l’activer, il suffit d’ajouter le fichier de déploiement de CDI
pour l’application. Ce fichier doit s’appeler `beans.xml` et être
situé dans le répertoire `WEB-INF`. Le contenu minimal de ce fichier est :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
  xmlns="http://java.sun.com/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/beans_1_0.xsd">
</beans>
```

## La vue : facelets

JSF n’utilise pas les JSP, JSF dispose de son propre langage de
déclaration de vue appelé **facelet**. Du point de vue du développeur,
nous allons voir qu’il n’y a pas une très grande différence entre une
JSP et une facelet. Par contre, il s’agit de deux technologies
différentes : les balises supportées ne sont pas les mêmes et une
facelet n’est pas transformée en Servlet.

Une facelet est un document XHTML 1.0 qui **doit** se conformer à la DTD
XHTML-1.0-Transitional. Avec le succès de HTML5, des adaptations ont été
faites dans JSF (Java EE 7) pour permettre de développer des facelets
HTML5. Néanmoins, une facelet doit être un document XML bien formé.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
  <p>Hello XHTML 1.0</p>
</body>
</html>
```

Une facelet est un document XML que le moteur JSF va analyser pour
rechercher des balises spécifiques JSF. L’utilisation des balises de
facelets se fait grâce aux espaces de nom XML (XML namespaces). Il
existe six bibliothèques standards de balises JSF. Chacune dispose de
son propre espace de nom XML.

| Préfixe   | XML namespace                                                         | Description                                                                                                                                                                                                                                                                                            | Exemples de balise                   |
|-----------|-----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| h         | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsf/html           | Contient les balises pour<br/>le rendu HTML des<br/>éléments pris en charge<br/>par JSF                                                                                                                                                                                                                | h:head h:body<br/>h:form h:inputText |
| f         | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsf/core           | Contient les balises qui<br/>ne génèrent pas de rendu<br/>HTML mais assurent<br/>l’interaction avec le<br/>serveur et le formatage<br/>de données                                                                                                                                                      | f:actionListener<br/>f:ajax          |
| c         | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsp/jstl/core      | Contient les balises de<br/>la bibliothèque JSTL core<br/>(Cf [cours sur les<br/>JSP](07_jsp.html#jstl)<br/>).<br/>**Attention** cette<br/>bibliothèque a été<br/>amputée notamment de la<br/>balise c:out par rapport<br/>à la version JSP. En JSF,<br/>on utilise<br/>`h:outputText` à la<br/>place. | c:if c:forEach                       |
| fn        | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsp/jstl/functions | Contient les fonctions de<br/>la bibliothèque JSTL<br/>functions (Cf [cours sur<br/>les<br/>JSP](07_jsp.html#jstl)<br/>).<br/>Cette bibliothèque ne<br/>contient pas de balise.                                                                                                                        | fn:contains<br/>fn:join              |
| ui        | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsf/facelets       | Contient les balises<br/>permettant des<br/>compositions de vues. Il<br/>est possible de définir<br/>un layout pour l’ensemble<br/>de l’application et<br/>d’appliquer<br/>automatiquement ce layout<br/>à chaque facelet.                                                                             | ui:component<br/>ui:composition      |
| cc        | [http://xmlns.jcp.org/](http://xmlns.jcp.org/)<br/>jsf/composite      | Permet de définir de<br/>nouveaux composants<br/>graphiques.                                                                                                                                                                                                                                           | cc:interface<br/>cc:implementation   |

La documentation des bibliothèques de balises est disponible sur
[https://docs.oracle.com/javaee/7/javaserver-faces-2-2/vdldocs-facelets/](https://docs.oracle.com/javaee/7/javaserver-faces-2-2/vdldocs-facelets/)

La bibliothèque JSF HTML ([http://xmlns.jcp.org/jsf/html](http://xmlns.jcp.org/jsf/html)) contient
notamment les balises
[h:head](https://docs.oracle.com/javaee/7/javaserver-faces-2-2/vdldocs-facelets/h/head.html),
[h:body](https://docs.oracle.com/javaee/7/javaserver-faces-2-2/vdldocs-facelets/h/body.html).
Ces balises permettent d’indiquer au moteur JSF les parties du document
HTML qui correspondent aux en-entêtes et au corps. JSF utilise ces
informations pour éventuellement enrichir la page XHTML finale avec des
balises supplémentaires.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <p>Hello Facelet</p>
</h:body>
</html>
```

**Une facelet est un fichier qui doit porter l’extension .xhtml**. En
effet, la servlet JSF est configurée pour traiter les requêtes de type
\*.xhtml. Ainsi, le serveur ne renvoie jamais les fichiers XHTML bruts.
Il délègue le traitement à la servlet JSF qui interprète la facelet.

On retrouve beaucoup de similitudes entre facelets et JSP. Par exemple,
voici une facelet affichant les paramètres de la requête HTTP :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:c="http://xmlns.jcp.org/jsp/jstl/core"
      xmlns:fn="http://xmlns.jcp.org/jsp/jstl/functions">
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <table>
    <tbody>
      <c:forEach var="entry" items="#{paramValues}">
        <tr>
          <td><h:outputText value="#{entry.key}"/></td>
          <td><h:outputText value="#{fn:join(entry.value, ', ')}" /></td>
        </tr>
      </c:forEach>
    </tbody>
  </table>
</h:body>
</html>
```

Il existe quasiment les mêmes [objets implicites](jsp.md#jsp-objets-implicites) accessibles avec
l’expression language (EL) que pour les JSP. Comme une JSP, une facelet
peut accéder aux attributs des différentes portées (page, request,
session, application). Cependant pour créer ces attributs, nous n’allons
plus utiliser l’API servlet comme vu précédemment, nous allons utiliser
le service **CDI**.

## Activer le support de JSF dans Eclipse

Eclipse supporte la complétion des balises JSF dans les fichiers de
facelets. Si vous ne disposez pas de cette fonctionnalité par défaut, il
vous faut peut-être activer manuellement le support de JSF pour votre
projet :

- dans l’explorateur de projet, faites un clique droit sur le nom du
  projet et sélectionnez « Properties » (ou ALT+Entrée),
- dans l’arborescence des propriétés, sélectionnez « Project Facets » et,
  dans la liste des Facets, cochez « Java Server Faces »,
- cliquez sur « OK ».

![Configuration de JSF dans Eclipse](javaee_web/assets/jsf/eclipse_configure_jsf.png)

## Une introduction à CDI

**Contexts and Dependency Injection** (CDI) est un service Java EE
permettant de déclarer des objets Java qui seront automatiquement créés
par le serveur et positionnés comme attributs dans la portée désirée.
Ensuite ces objets sont accessibles depuis une JSP ou une facelets ou
encore peuvent être injectés dans un composant Java EE ou un autre objet
géré par CDI.

## Déclarer un objet avec CDI

Nous verrons dans l’exemple ci-dessous une déclaration par annotations :

```java
import javax.enterprise.context.RequestScoped;
import javax.inject.Named;

@Named
@RequestScoped
public class PersonneControleur {

  public Personne getPersonne() {
    // ...
  }

}
```

L’annotation `@Named` suffit à indiquer que cette classe peut être
gérée par CDI. L’annotation `@RequestScoped` indique que l’instance de
l’objet sera un attribut de portée requête. Sur le même principe, il
existe les annotations `@SessionScoped` et `@ApplicationScoped`.

Par défaut, le nom de l’instance sera le même que le nom de la classe
commençant par une minuscule. Ainsi, une fois cette classe ajoutée dans
le projet, il est possible de l’utiliser dans une facelet :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <p>Bonjour #{personneControleur.personne.nom}</p>
</h:body>
</html>
```

S’il n’existe pas d’attribut `personneControleur`, celui-ci sera
automatiquement créé avec dans la portée spécifiée par annotation (dans la
portée requête pour l’exemple précédent).

Il est également possible de spécifier soi-même le nom du bean dans
l’annotation `@Named` :

```java
@Named("monControleurDePersonne")
```

#### NOTE
@SessionScoped et la passivation

Les classes Java portant l’annotation `@SessionScoped` doivent
supporter la passivation sinon vous obtiendrez une erreur à l’exécution.
La passivation signifie simplement que le contenu d’un objet doit
pouvoir être sauvé sur disque. Comme un objet de portée session a une
durée de vie qui dépasse le temps de traitement d’une requête HTTP, on
considère qu’il doit pouvoir être sauvé (passivation) lors de l’arrêt
d’un serveur et rechargé (activation) au démarrage afin de garantir la
continuité de service du point de vue du client. Sans aller trop loin
dans les implications de la passivation/activation, il suffit de savoir
que les classes annotées avec `@SessionScoped` doivent implémenter
l’interface marqueur
[java.io.Serializable](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/io/Serializable.html).

```java
import javax.enterprise.context.SessionScoped;
import javax.inject.Named;

@Named
@SessionScoped
public class PersonneControleur implements java.io.Serializable {

  public Personne getPersonne() {
    // ...
  }

}
```

## Le fichier de déploiement beans.xml

Comme la plupart des services Java EE, CDI dispose d’un fichier de
déploiement appelé `beans.xml`. Ce fichier sert à déclarer des
fonctionnalités avancées pour CDI mais il doit également être présent
dans l’arborescence de l’application pour indiquer au serveur d’applications d’activer le
service CDI pour cette application. Pour une application Web, le fichier
`beans.xml` doit se trouver dans le répertoire `WEB-INF`. Sa structure
minimale est :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
  xmlns="http://java.sun.com/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/beans_1_0.xsd">
</beans>
```

## Le modèle

Dans une application JSF, n’importe quelle instance d’objet Java peut
jouer le rôle du modèle. Le modèle peut être un objet géré par CDI ou
rendu disponible par un objet géré par CDI (ce dernier jouant alors le
rôle de contrôleur). Par exemple, une instance de la classe `Personne`
peut être utilisée comme modèle dans un formulaire d’une facelet en y
accédant *via* le bean CDI personneControleur vu précédemment :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <h:form acceptcharset="UTF-8" >
    <h:outputLabel for="nom" value="nom" />
    <h:inputText id="nom" value="#{personneControleur.personne.nom}"/>

    <h:outputLabel for="age" value="âge" />
    <h:inputText id="age" value="#{personneControleur.personne.age}" />

    <h:commandButton />
  </h:form>
</h:body>
</html>
```

Notez que depuis le début de ce chapitre, les expressions en EL
(expression language) utilisées dans les facelets sont délimitées par
**#{ }**. Comme pour les JSP, JSF supporte l’écriture d’une EL sous la
forme **${ }**. Cependant, l’utilisation du caractère **#** indique que
l’on souhaite activer le *value binding*. Cette fonctionnalité indique
au moteur JSF, que le contenu du bean `personneControleur.personne`
devra également être mis à jour avec les données envoyées par le client.
Concrètement, il est plus simple d’utiliser systématiquement avec JSF la
notation **#{ }**.

## Le contrôleur

JSF est basé sur l’API servlet mais il permet aux développeurs
d’application Web de s’en affranchir. Ainsi, avec JSF, un contrôleur est
simplement une classe Java gérée par CDI qui expose des méthodes qui
seront appelées par JSF lors de la réception des requêtes du client.
Dans la terminologie JSF, on parle de **backing beans** pour désigner
les objets Java avec lesquels la facelet iteragit.

La génération d’action vers le contrôleur se fait lorsque le client
envoie des données vers le serveur. En HTML, cela se fait par la
soumission de formulaire. Avec JSF, la soumission de formulaire peut se
faire avec la balise `h:commandLink` ou la balise `h:commandButton`.
Ces deux balise JSF disposent de l’attribut `action` qui permet
d’écrire une EL définissant l’appel à une fonction d’un backing bean
(une instance gérée par CDI). Il est possible de préciser dans l’EL les
paramètres qui seront passés à la méthode côté serveur.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <h:form acceptcharset="UTF-8" >
    <h:outputLabel for="nom" value="nom" />
    <h:inputText id="nom" value="#{personneControleur.personne.nom}"/>

    <h:outputLabel for="age" value="âge" />
    <h:inputText id="age" value="#{personneControleur.personne.age}" />

    <h:commandButton action="#{personneControleur.chercher()}"
                     value="chercher" />
  </h:form>
</h:body>
</html>
```

Dans la facelet ci-dessus, l’action déclenchée par le bouton « chercher »
est :

```jsp
#{personneControleur.chercher()}
```

Cela signifie que JSF va chercher un bean CDI portant le nom
« personneControleur » et il va invoquer la méthode `chercher`. Au
préalable, les attributs `nom` et `age` de la propriété
`personneControleur.personne` auront été mis à jour avec les valeurs
envoyées dans la requête.

Ainsi un contrôleur valide pourrait être :

```java
import javax.enterprise.context.RequestScoped;
import javax.inject.Named;

@Named
@RequestScoped
public class PersonneControleur {

  private Personne personne = new Personne();

  public Personne getPersonne() {
    return personne;
  }

  public void chercher() {
    String nomAChercher = personne.getNom();
    // ... effectuer la recherche dans un référentiel de personnes
  }

}
```

L’implémentation ci-dessus rappelle le modèle de conception
[Commande](https://fr.wikipedia.org/wiki/Commande_%28patron_de_conception%29).
À l’usage, c’est effectivement vers ce type de conception que souhaite
nous orienter les concepteurs de JSF.

## La navigation

Une fonctionnalité importante des frameworks Web est la gestion de la
navigation. Après avoir traité une requête, vers quelle vue, un
contrôleur doit-il déléguer le traitement pour construire la
représentation finale ?

Dans JSF, les vues sont les fichiers XHTML (les facelets). Les
identifiants des facelets correspondent simplement au nom du fichier
sans l’extension .xhtml. Une méthode de contrôleur indique la vue
résultat en retournant son identifiant. Si la méthode de contrôleur ne
retourne aucune valeur (void) ou retourne null, la vue résultat est la
vue courante.

Si nous reprenons notre exemple de contrôleur, nous pouvons indiquer la
vue résultat en modifiant la méthode `chercher` pour qu’elle retourne
une chaîne de caractères.

```java
import javax.enterprise.context.RequestScoped;
import javax.inject.Named;

@Named
@RequestScoped
public class PersonneControleur {

  private Personne personne = new Personne();

  public Personne getPersonne() {
    return personne;
  }

  public String chercher() {
    String nomAChercher = personne.getNom();
    // ... effectuer la recherche dans un référentiel de personnes

    // il doit exister un fichier resultat.xhtml qui correspond
    // à la facelet qui génèrera la vue.
    return "resultat";
  }

}
```

Pour la navigation par liens, il est possible d’utiliser les balises
`h:link` et `h:button` dans les facelets. Ces balises disposent de
l’attribut `outcome`. Cet attribut donne l’identifiant de la facelet
cible. Bien sûr, la valeur de l’attribut `outcome` peut être le
résultat d’une EL.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <ul>
    <!-- un lien vers la facelet entree.xhtml -->
    <li><h:link outcome="entree" value="Entrée"/></li>
    <!-- un lien vers la facelet plat.xhtml -->
    <li><h:link outcome="plat" value="Plat"/></li>
    <!-- un lien vers la facelet fromage.xhtml ou vers la facelet dessert.xhtml -->
    <li><h:link outcome="#{param['fromage'] ? 'fromage' : 'dessert'}"
                value="Fromage ou Dessert"/></li>
  </ul>
</h:body>
</html>
```

## La validation de formulaire

La validation des données de formulaire est une autre fonctionnalité
importante des frameworks Web. La bibliothèque de balises
[core](https://docs.oracle.com/javaee/7/javaserver-faces-2-2/vdldocs-facelets/f/tld-summary.html)
de JSF fournit, entre autres, les balises `f:validateDoubleRange`,
`f:validateLength`, `f:validateLongRange`, `f:validateRegex` et
`f:validateRequired`. Utilisées comme balises filles des entrées de
formulaire, elles permettent d’ajouter des règles de validité pour les
données de formulaire. JSF validera automatiquement les données soumises
par l’utilisateur avant de transférer le traitement au contrôleur.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://xmlns.jcp.org/jsf/core" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <h:form acceptcharset="UTF-8" >
    <h:outputLabel for="nom" value="nom" />
    <h:inputText id="nom" value="#{personneControleur.personne.nom}">
      <f:validateLength minimum="1"/>
    </h:inputText>
    <h:message for="nom"/>

    <h:outputLabel for="age" value="âge" />
    <h:inputText id="age" value="#{personneControleur.personne.age}">
      <f:validateLongRange minimum="1" maximum="99"/>
    </h:inputText>
    <h:message for="age"/>

    <h:commandButton action="#{personneControleur.chercher()}"
                     value="chercher" />
  </h:form>
</h:body>
</html>
```

Si la validation échoue, JSF retourne la même vue au client sans
solliciter le contrôleur. La vue dispose dans son contexte des messages
d’erreur de validation. La balise `h:message` permet d’indiquer où les
erreurs d’une entrée de formulaire seront affichées dans la réponse.

Les messages d’erreur de validation générés par JSF ne sont pas toujours
très adaptés. Pour surcharger les messages des validateurs, on peut
utiliser l’attribut `validatorMessage` sur les balises telles que
h:inputText, h:inputTextArea et h:inputSecret.

Il est également possible de redéfinir les messages par défaut de JSF
pour les adapter à son application. Pour plus d’information, vous pouvez
vous reporter à ce
[post](http://www.mkyong.com/jsf2/customize-validation-error-message-in-jsf-2-0/).

Il est également possible de fournir sa propre implémentation d’un
validateur. Pour cela, il suffit de créer une classe qui implémente
l’interface
[javax.faces.validator.Validator](https://docs.oracle.com/javaee/7/api/javax/faces/validator/Validator.html).
Cette interface ne contient la déclaration que d’une seule méthode :

```java
void validate(FacesContext context, UIComponent component, Object value) throws ValidatorException
```

La validation échoue si un appel à cette méthode lance une
[ValidatorException](https://docs.oracle.com/javaee/7/api/javax/faces/validator/ValidatorException.html).
Le premier paramètre représente le contexte d’exécution JSF, le deuxième
paramètre représente le composant graphique pour lequel la validation a
été demandée. Par exemple, pour un champ de formulaire de type
`input`, ce composant sera une instance de
[UIInput](https://docs.oracle.com/javaee/7/api/javax/faces/component/UIInput.html)
qui hérite de
[UIComponent](https://docs.oracle.com/javaee/7/api/javax/faces/component/UIComponent.html).
Enfin le troisième paramètre représente la valeur qui doit être validée.
Selon le type de composant, cette valeur peut être de type String,
Boolean…

La classe du validateur doit également porter l’annotation
[@FacesValidator](https://docs.oracle.com/javaee/7/api/javax/faces/validator/FacesValidator.html)
indiquant le nom du validateur qui sera utilisé pour le référencer dans
les facelets.

L’exemple ci-dessous montre un exemple de validateur permettant de
s’assurer qu’une case à cocher (checkbox) a bien été cochée par
l’utilisateur.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://xmlns.jcp.org/jsf/core" >
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <h:form acceptcharset="UTF-8" >
    <h:selectBooleanCheckbox id="myCheckbox" validatorMessage="Vous devez cocher la case">
      <f:validator validatorId="booleanValidator"/>
    </h:selectBooleanCheckbox>
    <h:outputLabel for="myCheckbox" value="case à cocher" />
    <h:message styleClass="error" for="myCheckbox"/><br/>

    <h:commandButton action="#{controleur.doSomething()}" value="Go" />
  </h:form>
</h:body>
</html>
```

L’implémentation de booleanValidator qui vérifie que la valeur vaut true
(et donc que la case a été cochée) :

```java
import javax.faces.application.FacesMessage;
import javax.faces.component.UIComponent;
import javax.faces.component.UIInput;
import javax.faces.context.FacesContext;
import javax.faces.validator.FacesValidator;
import javax.faces.validator.Validator;
import javax.faces.validator.ValidatorException;

// L'annotation ci-dessous permet de déclarer le nom du validateur pour JSF
@FacesValidator("booleanValidator")
public class BooleanValidator implements Validator {

  @Override
  public void validate(FacesContext ctx, UIComponent uiComponent, Object value)
      throws ValidatorException {
    // Puisque ce validateur est utilisé avec un h:selectBooleanCheckbox,
    // on s'attend à ce que value soit de type Boolean.
    if (! Boolean.TRUE.equals(value)) {
      // Si la valeur n'est pas true, le validateur signale une ValidatorException.
      // Le message d'erreur du validateur est directement extrait de l'attribut
      // validatorMessage du composant dans la facelet.
      UIInput uiInput = (UIInput) uiComponent;
      throw new ValidatorException(new FacesMessage(uiInput.getValidatorMessage()));
    }
  }

}
```

## La validation avec Bean Validation

Le serveur d’application fournit un autre service nommé
[Bean Validation](https://beanvalidation.org/) (JSR303). Bean Validation
permet d’exprimer les contraintes de validité d’un objet avec des
annotations. JSF est capable d’interagir avec Bean Validation pour la
validation de formulaire. Ainsi, plutôt que de déclarer la validation
dans une facelet comme dans la section précédente, il est possible
d’ajouter des annotations directement sur le bean `Personne` :

```java
import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.Size;

public class Personne {

  @Size(min = 1, message = "Le nom est obligatoire !")
  private String nom;

  @Min(value=1, message = "L'âge doit être un nombre positif !")
  @Max(value=99, message = "L'âge ne peut pas dépasser 99 ans !")
  private int age;

  public String getNom() {
    return nom;
  }

  public void setNom(String nom) {
    this.nom = nom;
  }

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }
}
```

La documentation des annotations de Bean Validation est disponible dans
la documentation de l’API Java EE :
[https://docs.oracle.com/javaee/7/api/javax/validation/constraints/package-summary.html](https://docs.oracle.com/javaee/7/api/javax/validation/constraints/package-summary.html)

Bean Validation est une bonne alternative aux balises JSF de validation
si un bean doit être réutilisé comme modèle dans des facelets
différentes.

## Les requêtes Ajax

#### NOTE
Vous pouvez télécharger un projet Maven de démonstration d’utilisation
d’Ajax dans JSF : [`jsf-demoajax.zip`](assets/templates/jsf-demoajax.zip)

Une requête Ajax est une requête asynchrone qui est exécutée par le
navigateur. Lorsque le serveur renvoie la réponse au navigateur, ce
dernier ne modifie pas la page affichée mais rend accessible la réponse
en JavaScript.

JSF supporte Ajax sans que le développeur Web n’ait à implémenter du
code JavaScript. JSF injecte lui-même le code JavaScript nécessaire au
moment du rendu de la facelet.

Pour activer Ajax, il suffit, par exemple, de spécifier la balise
`f:ajax` comme balise fille d’un `h:commandButton` :

```xml
<h:commandButton value="un bouton" action="#{monControleur.traiter()}">
  <f:ajax execute="@form" render="resultat" />
</h:commandButton>
```

La déclaration ci-dessus suffit à générer automatiquement le code
JavaScript dans la page XHTML pour que, lorsque l’utilisateur clique sur
le bouton, une requête Ajax soit soumise au serveur. La balise
`f:ajax` a deux attributs importants :

- `execute` : liste les composants qui sont pris en compte par la
  requête Ajax. Il est possible de lister les id des éléments d’un
  formulaire séparés par un espace. On peut aussi utiliser un des
  mots-clés suivants : `@form` (tous les éléments du formulaire courant),
  `@this` (uniquement le composant qui contient la balise `f:ajax`),
  `@all` (tous les composants graphiques JSF de la page), @none (aucune
  composant n’est associé à la requête Ajax).
- `render` : spécifie l’ID ou la liste des ID des composants
  graphiques JSF dans la facelet qui doivent être mis à jour lors de la
  réception de la réponse Ajax. Comme pour l’attribut précédent, il est
  possible d’utiliser les mots-clés `@this`, `@form`, `@all` et `@none`

Pour le support d’Ajax, l’implémentation du contrôleur se fait souvent
avec deux méthodes : une méthode pour permettre au contrôleur de
recevoir les données de la facelet et une méthode pour permettre à la
facelet d’obtenir, en retour, les résultats qui permettront de mettre à
jour une partie de la page.

La méthode du contrôleur spécifiée dans la balise `action` d’un
`h:commandButton` ne doit rien retourner (`void`) sinon JSF
interprétera le résultat comme un ID de facelet et redirigera le
navigateur vers une nouvelle page.

Un exemple simple mais complet serait :

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE html PUBLIC
          "-//W3C//DTD XHTML 1.0 Transitional//EN"
          "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html
  xmlns:f="http://xmlns.jcp.org/jsf/core"
  xmlns:h="http://xmlns.jcp.org/jsf/html">
<h:head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</h:head>
<h:body>
  <h:form>
    <h:commandButton value="un bouton" action="#{monControleur.traiter()}">
      <f:ajax execute="@form" render="resultat" />
    </h:commandButton>
  </h:form>
  <h:outputText id="resultat" value="#{monControleur.resultat}"/>
</h:body>
</html>
```

```java
import javax.enterprise.context.RequestScoped;
import javax.inject.Named;

@Named
@RequestScoped
public class MonControleur {

  private String resultat;

  public String getResultat() {
    return resultat;
  }

  public void traiter() {
    resultat = "Bravo, vous avez fait une requête Ajax !";
  }

}
```

## Pour aller plus loin

Ce chapitre n’avait pour ambition que de présenter les fonctionnalités
les plus essentielles de JSF. Avec JSF, vous avez aussi la possibilité
de créer un *layout* pour l’ensemble de l’application ou de créer en
facelet vos propres composants graphiques réutilisables.

Vous pouvez également enrichir votre application avec des composants
graphiques plus complexes. On pourra par exemple incorporer des
bibliothèques tierces comme la très impressionnante
[PrimeFaces](https://www.primefaces.org/).
