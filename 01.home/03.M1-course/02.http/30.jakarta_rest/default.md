---

title: Jakarta Restful Webservices

---

# Les API Web avec Jakarta RestFul Webservices

[Jakarta Restful Webservices](https://jakarta.ee/specifications/restful-ws/3.1/) est l’API conçue pour
implémenter des API Web (aussi appelées Web Services RESTful). Les API
Web exploitent les possibilités du protocole HTTP pour permettre à des
systèmes d’information de communiquer et de s’échanger des services.
Jakarta RESTFul Web Services est donc une API de programmation pour implémenter rapidement des
applications basées sur HTTP. Ainsi, comme nous le verrons plus loin,
son utilisation n’est pas réservée à l’implémentation de services mais
il peut tout aussi bien être utilisé pour développer des applications
Web plus traditionnelles.

Jakarta RESTFul Web Services 3.1 est l'API standard pour les webservices Rest de Jakarta EE 10 est défini par la spécification [Jakarta EE 10](https://jakarta.ee/specifications/restful-ws/3.1/). Comme pour tous les
services et toutes API Jakarta EE, il existe plusieurs implémentations de
cette spécification : [Jersey](ihttps://eclipse-ee4j.github.io/jersey)
(l’implémentation de référence),
[RestEasy](https://docs.resteasy.dev/) (intégré dans le serveur Wildfly et quarkus-rest).

## La notion de ressource

Dans le Web, ce qui est désigné par une URI est appelé une
**ressource**. Une ressource offre une interface uniforme qui est
définie dans HTTP par l’ensemble des méthodes : GET, HEAD, POST, PUT,
DELETE, OPTIONS… Un client HTTP positionne dans sa requête une méthode
pour indiquer le type d’opération que le serveur doit effectuer sur la
ressource.

[GET](https://tools.ietf.org/html/rfc7231#section-4.3.1)
: Demande au serveur une représentation de la ressource cible.

[HEAD](https://tools.ietf.org/html/rfc7231#section-4.3.2)
: Comme un GET sauf que la réponse ne contient jamais de corps. Cette
  méthode est utile pour obtenir les informations des en-têtes HTTP et
  valider une requête sans envoyer ni recevoir de corps de message.

[PUT](https://tools.ietf.org/html/rfc7231#section-4.3.4)
: Crée ou met à jour l’état d’une ressource identifiée par l’URI.

[DELETE](https://tools.ietf.org/html/rfc7231#section-4.3.5)
: Détruit l’association de l’URI avec l’état de la ressource.

[POST](https://tools.ietf.org/html/rfc7231#section-4.3.3)
: La sémantique de la méthode POST est probablement la plus compliquée
  à saisir car cette méthode est utilisable dans différentes
  situations.
  <br/>
  - Le client souhaite créer une ressource sur le serveur en laissant
    au serveur le choix de l’URI de la ressource.
  - Le client souhaite ajouter une ressource à une ressource
    représentant une collection.
  - Le client souhaite que le serveur effectue un traitement.
  - Le client souhaite modifier partiellement une ressource.

[OPTIONS](https://tools.ietf.org/html/rfc7231#section-4.3.7)
: Permet d’obtenir les options de communication (par exemple : les
  méthodes autorisées pour l’URI). Le serveur doit retourner ces
  informations dans les en-têtes de réponse. Ainsi l’en-tête de
  réponse
  [Allow](https://tools.ietf.org/html/rfc7231#section-7.4.1)
  liste les méthodes HTTP autorisées pour cette URI.

[TRACE](https://tools.ietf.org/html/rfc7231#section-4.3.8)
: Permet de simuler un écho de la requête. Cette méthode n’est pas
  utilisée pour la réalisation d’API Web car elle est surtout utile
  pour tester la configuration du réseau et obtenir des informations
  des proxies.

[CONNECT](https://tools.ietf.org/html/rfc7231#section-4.3.6)
: Établit un tunnel à travers un proxy. Cette méthode n’est pas
  utilisée pour la réalisation d’API Web.

## Configurer une application Web pour Jakarta RESTFul Web Services avec Quarkus

La configuration d'un enpoint pour une API Rest ne nécessite l'utilisation que d'une annotation: jakarta.ws.rs.Path.
Quarkus gère le cycle de vie de l'application et laisse le contexte CDI s'occuper de la mise en place des dépendences.
 

```java
@Path("jakartaee10")
public class JakartaEE10Resource {
    
    @GET
    public Response ping(){
        return Response
                .ok("ping Jakarta 10")
                .build();
    }
}
```


## Implémenter des ressources avec Jakarta RESTFul Web Services

Jakarta RESTFul Web Services permet d’implémenter des ressources sous la forme de composants
Jakarta EE. Une classe représentant une ressource est identifiée grâce à
l’annotation [@Path](https://jakarta.ee/specifications/restful-ws/3.1/apidocs/jakarta.ws.rs/jakarta/ws/rs/path).

[@Path](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Path.html)
: L’annotation `@javax.ws.rs.Path` indique le chemin d’URI qui
  identifie la ressource. Cette annotation est utilisable sur une
  classe et sur les méthodes. Utilisée sur une classe, cette
  annotation permet d’identifier la classe comme une ressource racine
  qui devient dès lors un composant géré par le serveur d’application.
  <br/>
  ```java
  import javax.ws.rs.Path;
  <br/>
  @Path("/user")
  public class UserResource {
  }
  ```
  <br/>
  Pour l’exemple ci-dessus, la ressource sera identifiée par l’URI :
  **http://[hôte]/[contexte racine]/[mapping servlet]/user**
  <br/>
  Utilisée sur une méthode, cette annotation permet de spécifier une
  sous-chemin dans la ressource. Si cette méthode retourne une classe
  utilisant des annotations Jakarta RESTFul Web Services, on parle alors de
  **sous-ressource**.
  <br/>
  ```java
  import javax.ws.rs.Path;
  <br/>
  @Path("/user")
  public class UserResource {
  <br/>
    @Path("/geo")
    public GeoLocation getGeographicalLocation() {
      //...
    }
  <br/>
  }
  ```
  <br/>
  Pour l’exemple ci-dessus, l’instance de la classe `GeoLocation`
  retournée par la méthode est accessible par l’URI :
  **http://[hôte]/[contexte racine]/[mapping servlet]/user/geo**
  <br/>
  Dans l’exemple précédent, si la classe `GeoLocation` utilise
  elle-même des annotations Jakarta RESTFul Web Services alors on dit qu’il s’agit d’une
  sous-ressource. Il devient possible de créer des arborescences de
  ressources en Java basées sur le chemin de l’URI.

### Les annotations de méthodes

Jakarta RESTFul Web Services fournit une annotation pour presque toutes les méthodes
HTTP :

- `@javax.ws.rs.GET`
- `@javax.ws.rs.HEAD`
- `@javax.ws.rs.POST`
- `@javax.ws.rs.PUT`
- `@javax.ws.rs.DELETE`
- `@javax.ws.rs.OPTIONS`

Elles permettent d’indiquer quelle méthode Java doit être appelée
pour traiter la méthode de la requête HTTP entrante.

```java
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;

@Path("/user")
public class UserResource {

  @GET
  public User get() {
    //....
  }

  @PUT
  public User createOrUpdate() {
    //....
  }

  @DELETE
  public void delete() {
    //....
  }

  @POST
  @Path("/subscription")
  public void subscribe() {
    //....
  }

}
```

Si aucune méthode Java n’est déclarée pour traiter la méthode HTTP
de la requête entrante, alors le serveur répondra automatiquement le
code erreur `405` (Method not allowed) sauf pour les méthodes
`HEAD` et `OPTIONS`. Pour la méthode HTTP `HEAD`, Jakarta RESTFul Web Services tente
d’appeler la méthode Java associée à `GET` et ignore le corps de
la réponse (ce qui est exactement le comportement attendu par un
client HTTP qui effectue ce type de requête). Pour la méthode HTTP
`OPTIONS`, Jakarta RESTFul Web Services génère une réponse contenant l’en-tête `Allow`
donnant la liste des méthodes HTTP autorisées pour cette ressource
en se basant sur les annotations Jakarta RESTFul Web Services présentes dans la classe.

### Paramètre dans le chemin d’URI

Comme chaque ressource Web est identifiée par une URI, il est
important pour le serveur de pouvoir récupérer dans le chemin les
informations qui vont lui permettre de réaliser cette identification
dynamiquement. Par exemple, le serveur peut extraire du chemin de la
ressource une clé primaire lui permettant d’effectuer une recherche
en base de données.

Avec Jakarta RESTFul Web Services, on déclare des paramètres de chemin entre accolades et
on utilise l’annotation `javax.ws.rs.PathParam` pour récupérer
leur valeur dans les paramètres des méthodes :

```java
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;

@Path("/user/{id}")
public class UserResource {

  @GET
  public User get(@PathParam("id") long id) {
    //....
  }

  @PUT
  public User createOrUpdate(@PathParam("id") long id, User user) {
    //....
  }

  @DELETE
  public void delete(@PathParam("id") long id) {
    //....
  }

  @POST
  @Path("/subscription")
  public void subscribe(@PathParam("id") long id) {
    //....
  }

  @GET
  @Path("/subscription/{idSubscription}")
  public Subscription getSubscription(@PathParam("id") long id,
                                      @PathParam("idSubscription") String idSub) {
    //....
  }
}
```

Jakarta RESTFul Web Services est une API très versatile. Elle autorise beaucoup plus de
souplesse que la plupart des autres API Jakarta EE. Pour l’exemple
précédent, comme le paramètre `{id}` permettant d’identifier un
utilisateur est déclaré au niveau de la classe, on devrait pouvoir
obtenir cet identifiant à la construction de l’instance. Jakarta RESTFul Web Services
permet effectivement cette implémentation qui semble plus conforme à
un modèle objet :

```java
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;

@Path("/user/{id}")
public class UserResource {

  private final long id;

  public UserResource(@PathParam("id") long id) {
    this.id = id;
  }

  @GET
  public User get() {
    // ...
  }

  @PUT
  public User createOrUpdate(User user) {
    // ...
  }

  @DELETE
  public void delete() {
    //....
  }

  @POST
  @Path("/subscription")
  public void subscribe() {
    //....
  }

  @GET
  @Path("/subscription/{idSubscription}")
  public Subscription getSubscription(@PathParam("idSubscription") String idSub) {
    //....
  }
}
```

Contrairement à l’API Servlet, l’API Jakarta RESTFul Web Services **crée une instance** de
`UserResource` pour chaque requête. Il est donc possible de
stocker dans l’état de l’instance des informations spécifiques à la
requête (comme l’identifiant de l’utilisateur).

Jakarta RESTFul Web Services peut réaliser le transtypage d’un paramètre de chemin vers
les types primitifs et les chaînes de caractères. Cela permet de
garantir un premier contrôle de la validité de la donnée. Si la
valeur attendue doit avoir un motif particulier, il est possible de
le spécifier avec une expression régulière :

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;

@Path("/user/{id: [0-9]{5}}")
public class UserResource {

  private final long id;

  public UserResource(@PathParam("id") long id) {
    this.id = id;
  }

  @GET
  public User get() {
    // ...
  }

}
```

Par défaut, Jakarta RESTFul Web Services utilise comme expression régulière pour un
paramètre de chemin `[^/]+?`

[@Consumes](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Consumes.html) / [@Produces](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Produces.html)
: Lorsqu’un client soumet une requête pour transmettre des
  informations au serveur (comme des données de formulaire) et quand
  un serveur retourne du contenu à un client, il est nécessaire de
  préciser le type de contenu. On utilise pour cela l’en-tête HTTP
  `Content-type` avec comme valeur le type
  [MIME](https://fr.wikipedia.org/wiki/Type_MIME).
  <br/>
  Une liste (non exhaustive) des types MIME les plus courants est :
  <br/>
  | text/plain                        | Un fichier texte                                                                                               |
  |-----------------------------------|----------------------------------------------------------------------------------------------------------------|
  | text/plain;charset=utf-8          | Un fichier texte encodé en UTF-8                                                                               |
  | text/html                         | Un fichier HTML                                                                                                |
  | application/x-www-form-urlencoded | Le format de données pour la soumission d’un formulaire HTML                                                   |
  | text/xml ou application/xml       | Un fichier XML                                                                                                 |
  | text/json ou application/json     | Un fichier JSON                                                                                                |
  | image/jpeg                        | Une image au format jpeg                                                                                       |
  | application/octet-stream          | Un flux d’octets sans type particulier. Il s’agit du format par défaut si l’en-tête `Content-type` est absent. |
  <br/>
  La classe et/ou les méthodes d’une Ressource Jakarta RESTFul Web Services peuvent utiliser
  les annotations
  [@Consumes](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Consumes.html)
  et
  [@Produces](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Produces.html)
  pour indiquer respectivement le type de contenu attendu dans la
  requête et le type de contenu de la réponse.
  <br/>
  ```java
  import javax.ws.rs.DELETE;
  import javax.ws.rs.GET;
  import javax.ws.rs.POST;
  import javax.ws.rs.PUT;
  import javax.ws.rs.Path;
  import javax.ws.rs.PathParam;
  import javax.ws.rs.Produces;
  import javax.ws.rs.Consumes;
  import javax.ws.rs.core.MediaType;
  <br/>
  @Path("/user/{id}")
  public class UserResource {
  <br/>
    private final long id;
  <br/>
    public UserResource(@PathParam("id") long id) {
      this.id = id;
    }
  <br/>
    @GET
    @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
    public User get() {
      // ...
    }
  <br/>
    @PUT
    @Consumes({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
    @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
    public User createOrUpdate(User user) {
      // ...
    }
  <br/>
    @DELETE
    public void delete() {
      //....
    }
  <br/>
    @POST
    @Path("/subscription")
    public void subscribe() {
      //....
    }
  <br/>
    @GET
    @Path("/subscription/{idSubscription}")
    @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
    public Subscription getSubscription(@PathParam("idSubscription") String idSub) {
      //....
    }
  }
  ```
  <br/>
  Plutôt que d’écrire :
  <br/>
  ```java
  @Produces("application/json")
  ```
  <br/>
  Il est recommandé d’utiliser les constantes déclarées dans la classe
  `javax.ws.rs.core.MediaType`
  <br/>
  ```java
  @Produces(MediaType.APPLICATION_JSON)
  ```

[@QueryParam](https://docs.oracle.com/javaee/7/api/javax/ws/rs/QueryParam.html)
: Comme pour les paramètres de chemin, il est possible de récupérer la
  valeur des paramètres de la requête comme arguments des méthodes de
  la ressource Jakarta RESTFul Web Services grâce à l’annotation
  `@javax.ws.rs.QueryParam`.
  <br/>
  ```java
  @GET
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public List<User> search(@QueryParam("name") String name) {
    // ...
  }
  ```

[@FormParam](https://docs.oracle.com/javaee/7/api/javax/ws/rs/FormParam.html)
: Les données transmises *via* un formulaire HTML peuvent être
  récupérées comme arguments des méthodes de la ressource Jakarta RESTFul Web Services grâce
  à l’annotation `@javax.ws.rs.FormParam`. Pour le cas d’une requête
  de formulaire, le contenu attendu est presque toujours de type
  `application/x-www-form-urlencoded`.
  <br/>
  ```java
  @POST
  @Consumes(MediaType.APPLICATION_FORM_URLENCODED)
  public void create(@FormParam("name") String name, @FormParam("age") int age) {
    // ...
  }
  ```
  <br/>
  Sur le même principe, il est également possible de récupérer
  d’autres informations d’une requête :
  <br/>
  - Pour récupérer la valeur d’un en-tête HTTP, il faut utiliser
    l’annotation `@javax.ws.rs.HeaderParam`
  - Pour récupérer la valeur d’un Cookie HTTP, il faut utiliser
    l’annotation `@javax.ws.rs.CookieParam`

[@Context](https://docs.oracle.com/javaee/7/api/javax/ws/rs/core/Context.html)
: Si vous avez besoin d’obtenir des informations sur le contexte
  d’exécution de la requête, vous pouvez utilisez l’annotation
  `@javax.ws.rs.core.Context` pour obtenir une instance d’une
  classe particulière. Les classes supportées sont :
  <br/>
  - `javax.ws.rs.core.UriInfo` : Cette interface donne accès à
    l’URI de la requête.
  - `javax.ws.rs.core.Request` : Cette interface fournit des
    méthodes utilitaires pour le traitement conditionnel de la
    requête.
  - `javax.ws.rs.core.HttpHeaders` : Cette interface permet
    d’accéder à l’ensemble des en-têtes HTTP de la requête.
  - `javax.ws.rs.core.SecurityContext` : Cette interface permet
    d’accéder aux informations de sécurité et d’authentification.
  - `javax.servlet.http.HttpServletRequest` : La représentation de
    la requête avec l’API Servlet.
  - `javax.servlet.http.HttpServletResponse` : La représentation de
    la réponse avec l’API Servlet.
  - `javax.servlet.ServletContext` : Le contexte d’exécution des
    servlets.
  - `javax.servlet.ServletConfig` : La configuration de la servlet
    traitant la requête.
  <br/>
  ```java
  @GET
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public List<User> search(@Context UriInfo uriInfo, @Context Request req) {
    // ...
  }
  ```
  <br/>
  Pour des utilisations plus avancées, l’annotation
  `@javax.ws.rs.core.Context` peut être utilisée pour injecter
  une instance de `javax.ws.rs.core.Application`, de
  `javax.ws.rs.ext.Providers` et de
  `javax.ws.rs.ext.ContextResolver<T>`.

## Data binding

Lorsqu’une méthode d’une ressource retourne une instance d’un objet
Java, Jakarta RESTFul Web Services va tenter de créer une réponse au format souhaité en
fonction de l’annotation
[@Produces](https://docs.oracle.com/javaee/7/api/javax/ws/rs/Produces.html).
Il existe un ensemble de règles par défaut permettant de passer d’un
objet Java à un document XML ou JSON. On appelle l’ensemble de ces règle
le **data binding**.

Si la réponse attentue est au format JSON alors Jakarta RESTFul Web Services va construire une
réponse en se basant sur les accesseurs (les getters) de la classe.

Si on souhaite retourner une instance de la classe suivante :

```java
import java.util.ArrayList;
import java.util.List;

public class Person {

  private String name;
  private int age;
  private List<Person> children = new ArrayList<>();

  public Person() {
  }

  public Person(String name, int age) {
    this.name = name;
    this.setAge(age);
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  public List<Person> getChildren() {
    return children;
  }

  public Person addChild(Person child) {
    this.children.add(child);
    return child;
  }
}
```

Si on définit une ressource de la façon suivante :

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/person")
public class PersonResource {

  @GET
  @Produces(MediaType.APPLICATION_JSON)
  public Person get() {
    Person michel = new Person("Michel Raynaud", 56);
    michel.addChild(new Person("Anne Raynaud", 38)).addChild(new Person("Pierre Blémand", 16));
    michel.addChild(new Person("Damien Raynaud", 32));
    return michel;
  }
}
```

Alors un appel HTTP à cette ressource génèrera un document JSON de la
forme :

```json
{"children":[
  {"children":[
    {"children":[],
     "name":"Pierre Blémand",
     "age":16}
   ],
   "name":"Marie Raynaud",
   "age":38},
  {"children":[],
   "name":"Damien Raynaud",
   "age":32}
 ],
 "name":"Michel Raynaud",
 "age":56}
```

Il est également possible de réaliser l’opération inverse pour récupérer
en paramètre un document JSON transformé en une instance Java.

```java
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.Consumes;
import javax.ws.rs.core.MediaType;

@Path("/person")
public class PersonResource {

  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  public void post(Person person) {
    // ...
  }
}
```

Il est également possible de passer d’une instance Java à un document
XML ou d’un document XML à une instance Java. Pour cela, Jakarta RESTFul Web Services utilise
[JAXB](https://github.com/javaee/jaxb-v2) (Java Architecture for XML
Binding) qui intégré au langage Java. JAXB utilise des annotations pour
fournir des indications sur la façon dont une classe Java peut être
associée à un document XML.

Les principales annotations JAXB sont :

[@XmlRootElement](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlRootElement.html)
: Une annotation est utilisable sur une classe Java pour indiquer
  quelle peut être utilisée pour représenter la racine d’un document
  XML. On peut utiliser l’attribut `name` de l’annotation pour
  préciser le nom de l’élément racine du document XML et l’attribut
  `namespace` pour en préciser l’espace de nom.

[@XmlElement](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlElement.html)
: Une annotation est utilisable sur les accesseurs (getters) des
  propriétés d’une classe. On peut utiliser l’attribut `name` de
  l’annotation pour préciser le nom de l’élément racine du document
  XML et l’attribut `namespace` pour en préciser l’espace de nom.
  Cette annotation est optionnelle. Par défaut JAXB considère qu’une
  propriété produit un élément XML du même nom et sans espace de nom
  XML.

[@XmlTransient](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlTransient.html)
: Cette annotation, ajoutée sur les accesseurs d’une propriété d’une
  classe, indique que cette propriété ne doit pas apparaître dans le
  document XML.

#### WARNING
Les annotations JAXB doivent être positionnées sur les *getters* et non
pas sur les attributs.

Si nous reprenons l’exemple de la classe `Person`, nous pouvons
ajouter les annotations JAXB :

```java
import java.util.ArrayList;
import java.util.List;

import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlElementWrapper;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement(name="person", namespace="http://formation.fr/cours/javaee")
public class Person {

  private String name;
  private int age;
  private List<Person> children = new ArrayList<>();

  public Person() {
  }

  public Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @XmlElement(namespace="http://formation.fr/cours/javaee")
  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  @XmlElement(namespace="http://formation.fr/cours/javaee")
  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  @XmlElement(name="person", namespace="http://formation.fr/cours/javaee")
  @XmlElementWrapper(name="children", namespace="http://formation.fr/cours/javaee")
  public List<Person> getChildren() {
    return children;
  }

  public Person addChild(Person child) {
    this.children.add(child);
    return child;
  }
}
```

Si nous autorisons une ressource à produire du XML :

```java
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/person")
public class PersonResource {

  @GET
  @Produces(MediaType.APPLICATION_XML)
  public Person get() {
    Person michel = new Person("Michel Raynaud", 56);
    michel.addChild(new Person("Anne Raynaud", 38)).addChild(new Person("Pierre Blémand", 16));
    michel.addChild(new Person("Damien Raynaud", 32));
    return michel;
  }
}
```

Alors un appel HTTP à cette ressource générera un document JSON de la
forme :

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<person xmlns="http://formation.fr/cours/javaee">
  <age>56</age>
  <children>
    <person>
      <age>38</age>
      <children>
        <person>
          <age>16</age>
          <children/>
          <name>Pierre Blémand</name>
        </person>
      </children>
      <name>Anne Raynaud</name>
    </person>
    <person>
      <age>32</age>
      <children/>
      <name>Damien Raynaud</name>
    </person>
  </children>
  <name>Michel Raynaud</name>
</person>
```

Il est possible d’indiquer dans les annotations `@Produces` et
`@Consumes` plusieurs formats supportés. Pour la génération de la
réponse, Jakarta RESTFul Web Services utilise le mécanisme de la négociation de contenu HTTP
pour déterminer quel est le format à utiliser pour la réponse.

```java
import javax.ws.rs.Consumes;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;

@Path("/person")
public class PersonResource {

  @GET
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public Person get() {
    // ...
  }

  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public void post(Person person) {
    // ...
  }
}
```

Les annotations JAXB sont également exploitées pour la génération d’un
document JSON. Par exemple si vous utilisez l’annotation `@XmlElement`
pour spécifier un nom particulier pour l’élément XML, l’attibut JSON
aura également le même nom.

## Générer une réponse

Parfois, il n’est pas suffisant de retourner une instance d’un objet
Java en laissant à Jakarta RESTFul Web Services le soin de créer la réponse HTTP. C’est
notamment le cas si l’on souhaite retourner un code statut HTTP
différent de 200 ou ajouter des en-têtes HTTP dans la réponse. Pour
cela, il faut retourner une instance de la classe
[javax.rs.core.Response](https://docs.oracle.com/javaee/7/api/javax/ws/rs/core/Response.html).
Cette classe suit le *design pattern builder* et offre un ensemble de
méthodes utilitaires pour construire la réponse. Au final, il suffit
d’appeler la méthode `build()` et retourner le résultat.

```java
import java.net.URI;

import javax.ws.rs.Consumes;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.Context;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.UriInfo;

@Path("/person")
public class PersonResource {

  @GET
  @Path("/{name}")
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public Response get(@PathParam("name") String name) {
    Person person;

    // ...

    return Response.ok(person).build();
  }

  @POST
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces({MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML})
  public Response create(Person person, @Context UriInfo uriInfo) {

    // ... on sauvegarde la représentation de la personne

    // on construit l'URI correspondant à la personne
    URI location = uriInfo.getRequestUriBuilder()
                          .path(person.getName())
                          .build();

    // On retourne la réponse
    return Response.created(location).entity(person).build();
  }
}
```

## Gérer des exceptions

Par défaut, si une méthode d’une ressource génère une exception, alors
Jakarta RESTFul Web Services la transforme en erreur HTTP 500. Si l’on souhaite retourner un
statut d’erreur différent, il est bien évidemment possible d’utiliser la
classe
[javax.rs.core.Response](https://docs.oracle.com/javaee/7/api/javax/ws/rs/core/Response.html),
mais il est plus intéressant de fournir les indications nécessaires à
Jakarta RESTFul Web Services pour modifier son comportement selon le type d’exception lancé
par la méthode de la ressource.

Il est possible de lancer une exception de type
[WebApplicationException](https://docs.oracle.com/javaee/7/api/javax/ws/rs/WebApplicationException.html)
ou une exception en héritant. Jakarta RESTFul Web Services fournit déjà des exceptions
spécialisées pour les codes de statut les plus courants :
[NotFoundException](https://docs.oracle.com/javaee/7/api/javax/ws/rs/NotFoundException.html),
[BadRequestException](https://docs.oracle.com/javaee/7/api/javax/ws/rs/BadRequestException.html),
[ServerErrorException](https://docs.oracle.com/javaee/7/api/javax/ws/rs/ServerErrorException.html)…
et même la possibilité de traiter les redirections avec l’exception
[RedirectionException](https://docs.oracle.com/javaee/7/api/javax/ws/rs/RedirectionException.html).

Il est également possible de déclarer une classe implémentant
l’interface
[ExceptionMapper](https://docs.oracle.com/javaee/7/api/javax/ws/rs/ext/ExceptionMapper.html).
Un `ExceptionMapper` est déclaré pour un type d’exception et ses
exceptions filles.

```java
import javax.validation.ValidationException;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.Response.Status;
import javax.ws.rs.ext.ExceptionMapper;
import javax.ws.rs.ext.Provider;

@Provider
public class ValidationExceptionMapper implements ExceptionMapper<ValidationException>{

  @Override
  public Response toResponse(ValidationException exception) {
    return Response.status(Status.BAD_REQUEST)
                   .type(MediaType.TEXT_PLAIN)
                   .entity(exception.getMessage())
                   .build();
  }

}
```

Dans l’exemple ci-dessus, tout appel à une méthode de ressource qui se
terminera par une exception de type `ValidationException` entraînera
un appel de la méthode `ValidationExcceptionMapper.toResponse` qui
générera une réponse de type 400 (Bad Request) avec un message en texte
brut correspondant au message de l’exception.

Notez l’utilisation de l’annotation
[@Provider](https://docs.oracle.com/javaee/7/api/javax/ws/rs/ext/Provider.html)
dans l’exemple précédent. Cette annotation est utilisée dans Jakarta RESTFul Web Services pour
signaler des classes utilitaires qui permettent d’étendre le
comportement par défaut de Jakarta RESTFul Web Services.

## La validation avec Bean Validation

Le serveur d’application fournit un service nommé [Bean Validation](https://beanvalidation.org/) (JSR303).
Bean Validation permet d’exprimer les contraintes de validité d’un objet ou des
paramètres d’une méthode de ressource avec des annotations. Jakarta RESTFul Web Services
utilise les informations de ces annotations pour valider les requêtes
HTTP.

```java
import java.util.ArrayList;
import java.util.List;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import javax.validation.constraints.Size;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlElementWrapper;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement(name="person", namespace="http://formation.fr/cours/javaee")
public class Person {

  @Size(min = 1, message = "Le nom est obligatoire !")
  private String name;

  @Min(value=1, message = "L'âge doit être un nombre positif !")
  @Max(value=99, message = "L'âge ne peut pas dépasser 99 ans !")
  private int age;

  private List  <Person> children = new ArrayList  <>();

  public Person() {
  }

  public Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @XmlElement(namespace="http://formation.fr/cours/javaee")
  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  @XmlElement(namespace="http://formation.fr/cours/javaee")
  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  @XmlElement(name="person", namespace="http://formation.fr/cours/javaee")
  @XmlElementWrapper(name="children", namespace="http://formation.fr/cours/javaee")
  public List  <Person> getChildren() {
    return children;
  }

  public Person addChild(Person child) {
    this.children.add(child);
    return child;
  }
}
```

```java
import javax.validation.constraints.Size;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;

@Path("/person")
public class PersonResource {

  @GET
  @Path("/{name}")
  @Produces({ MediaType.APPLICATION_JSON, MediaType.APPLICATION_XML })
  public Response get(
      @Size(min = 1, message = "Chemin de ressource invalide !")
      @PathParam("name") String name) {
    Person person;

    // ...

    return Response.ok(person).build();
  }
}
```

La documentation des annotations de Bean Validation est disponible dans
la documentation de l’API Jakarta EE :
[https://docs.oracle.com/javaee/7/api/javax/validation/constraints/package-summary.html](https://docs.oracle.com/javaee/7/api/javax/validation/constraints/package-summary.html)

## Implémenter un client HTTP

Jakarta RESTFul Web Services fournit également une API pour implémenter un client HTTP. On
utilise la classe
[ClientBuilder](https://docs.oracle.com/javaee/7/api/javax/ws/rs/client/ClientBuilder.html)
pour créer une instance de la classe
[Client](https://docs.oracle.com/javaee/7/api/javax/ws/rs/client/Client.html).

```java
import javax.ws.rs.client.Client;
import javax.ws.rs.client.ClientBuilder;
import javax.ws.rs.client.WebTarget;

public class ExempleClient {

  public static void main(String[] args) {
    Client client = ClientBuilder.newClient();

    WebTarget target = client.target("http://www.server.net/person");
    Person person = target.request().get(Person.class);

    // ...
  }
}
```
