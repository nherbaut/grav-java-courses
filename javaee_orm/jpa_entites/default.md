# Les entités JPA

Nous avons vu que l’API JDBC nous permet d’écrire des programmes Java
qui interagissent avec des bases de données. JDBC nous assure que le
code Java sera semblable quel que soit le SGBDR utilisé (mais le code
SQL pourra bien sûr être différent en exploitant telle ou telle
fonctionnalité non standard fournie par le SGBDR).

Néanmoins, JDBC a quelques inconvénients :

- l’API est verbeuse et répétitive. Pour un programme de quelques
  centaines de lignes de code, elle se révèle très efficace. Mais pour
  des applications plus volumineuses, la quantité de code nécessaire
  (notamment SQL) peut devenir une source de ralentissement du
  développement.
- la gestion des nombreuses ressources ([Connection](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/Connection.html), [Statement](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/Statement.html),
  [ResultSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/ResultSet.html)) est une source permanente de bugs pour les
  développeurs. Il est donc très facile d’écrire des applications qui
  perdent des ressources.
- JDBC n’offre qu’un service limité : un système d’échange avec une
  base de données (même s’il le fait très bien).

## Les ORM (Object-Relational Mapping)

Les ORM sont des frameworks qui, comme l’indique leur nom, permettent de
créer une correspondance entre un modèle objet et un modèle relationnel
de base de données. Un ORM fournit généralement les fonctionnalités
suivantes :

- génération à la volée des requêtes SQL les plus simples (CRUD)
- prise en charge des dépendances entre objets pour la mise en jour en
  cascade de la base de données
- support pour la construction de requêtes complexes par programmation

Java EE fournit une API standard pour l’utilisation d’un ORM : **JPA
(Java Persistence API)** (la [JSR-338](https://jcp.org/aboutJava/communityprocess/final/jsr338/index.html)). Il existe plusieurs implémentations
open source qui respectent l’API JPA :
[EclipseLink](https://www.eclipse.org/eclipselink/) (qui est aussi l’implémentation de référence), [Hibernate](http://hibernate.org/)
(JBoss - Red Hat), [OpenJPA](http://openjpa.apache.org/) (Apache).

Toutes ces implémentations sont bâties sur JDBC. Nous retrouverons donc
les notions de pilote, de data source et d’URL de connexion lorsqu’il
s’agira de configurer l’accès à la base de données.

#### NOTE
Pour ce cours, nous utiliserons comme exemple l’implémentation
fournie par [Hibernate](http://hibernate.org/). À version identique, le code présenté devrait être
compatible avec les autres implémentations de JPA.

## Les entités JPA

JPA permet de définir des entités (*entities*). Une entité est
simplement une instance d’une classe qui sera *persistante* (que l’on
pourra sauvegarder dans / charger depuis une base de données
relationnelle). Une entité est signalée par l’annotation [@Entity](https://docs.oracle.com/javaee/7/api/javax/persistence/Entity.html)
sur la classe. De plus, une entité JPA **doit disposer d’un ou plusieurs
attributs définissant un identifiant** grâce à l’annotation [@Id](https://docs.oracle.com/javaee/7/api/javax/persistence/Id.html).
Cet identifiant correspondra à la clé primaire dans la table associée.

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Individu {

    @Id
    // Permet de définir la statégie de génération
    // de la clé lors d'une insertion en base de données.
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }
}
```

Il existe un grand nombre d’annotations JPA servant à préciser comment
la correspondance doit être faite entre le modèle objet et le modèle
relationnel de base de données. Il est possible de déclarer cette
correspondance à l’aide du fichier XML `orm.xml`. Cependant, la
plupart de développeurs préfèrent utiliser des annotations.

La liste ci-dessous résument les annotations les plus simples et les plus utiles
pour définir une entité et ses attributs :

[@Entity](https://docs.oracle.com/javaee/7/api/javax/persistence/Entity.html)
: Définit qu’une classe est une entité. Le nom de l’entité est donné par
  l’attribut `name` (en son absence le nom de l’entité correspond au nom
  de la classe).

[@Id](https://docs.oracle.com/javaee/7/api/javax/persistence/Id.html)
: Définit l’attribut qui sert de clé primaire dans la table. Il est recommandé
  au départ d’utiliser un type primitif, un wrapper de type primitif ou une
  [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html) pour représenter un id. Pour les clés composites,
  la mise en œuvre est plus compliquée. Afin  de ne pas se compliquer
  inutilement la tâche, il vaut mieux prévoir une clé simple pour chaque entité.

[@Basic](https://docs.oracle.com/javaee/7/api/javax/persistence/Basic.html)
: Définit un mapping simple pour un attribut (par exemple `VARCHAR` pour
  [String](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html)). Si on ne souhaite pas changer la valeur des attributs par défaut
  de cette annotation, alors il est possible de ne pas la spécifier
  puisqu’elle représente le mapping par défaut.

[@Temporal](https://docs.oracle.com/javaee/7/api/javax/persistence/Temporal.html)
: Pour un attribut de type [java.util.Date](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Date.html) et [java.util.Calendar](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Calendar.html), cette
  annotation permet de préciser le type de mapping vers le type SQL (DATE,
  TIME ou TIMESTAMP).

[@Transient](https://docs.oracle.com/javaee/7/api/javax/persistence/Transient.html)
: Indique qu’un attribut **ne doit pas** être persistant. Cet attribut ne sera
  donc jamais pris en compte lors de l’exécution des requêtes vers la base de données.

[@Lob](https://docs.oracle.com/javaee/7/api/javax/persistence/Lob.html)
: Indique que la colonne correspondante en base de données est un LOB (large object).

Certaines annotations sont utilisées pour fournir des informations sur la base
de données sous-jacente :

[@Table](https://docs.oracle.com/javaee/7/api/javax/persistence/Table.html)
: Permet de définir les informations sur la table représentant cette entité
  en base de données. Il est possible de définir le nom de la table grâce à
  l’attribut `name`. Par défaut le nom de la table correspond au nom de
  l’entité (qui par défaut correspond au nom de la classe).

[@GeneratedValue](https://docs.oracle.com/javaee/7/api/javax/persistence/GeneratedValue.html)
: Indique la stratégie à appliquer pour la génération de la clé lors de
  l’insertion d’une entité en base. Les valeurs possibles sont données par
  l’énumération [GenerationType](https://docs.oracle.com/javaee/7/api/javax/persistence/GenerationType.html).
  Si vous utilisez MySQL et la propriété `autoincrement` sur une colonne,
  alors vous devez utiliser `GenerationType.IDENTITY` (ce sera le cas pour
  les exemples de ce cours).
  Si vous utilisez Oracle et un système de séquence, alors vous devez utiliser
  `GenerationType.SEQUENCE` et préciser le nom de la séquence dans
  l’attribut `generator` de `@GeneratedValue`.

[@Column](https://docs.oracle.com/javaee/7/api/javax/persistence/Column.html)
: Permet de déclarer des informations relatives à la colonne sur laquelle un
  attribut doit être mappé. Si cette annotation est absente, le nom de la
  colonne correspond au nom de l’attribut. Avec cette annotation, il est
  possible de donner le nom de la colonne (l’attribut `name`) mais également
  si l’attribut doit être pris en compte pour des requêtes d’insertion
  (l’attribut `insertable`) ou de mise à jour (l’attribut `updatable`).
  Certains outils sont capables d’exploiter les annotations pour créer les
  bases de données. Dans ce cas, d’autres attributs sont disponibles pour
  ajouter toutes les contraintes nécessaires (telles que `length` ou
  `nullable`) et donner ainsi une description complète de la colonne.

```java
import java.util.Calendar;
import javax.persistence.Basic;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Lob;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;
import javax.persistence.Transient;

@Entity
@Table(name="individu")
public class Individu {

    @Id
    @Column(name="individuId")
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;

    @Basic
    @Column(length=30, nullable=false)
    private String nom;

    @Basic
    @Column(length=30, nullable=false)
    private String prenom;

    @Transient
    private Integer age;

    @Temporal(TemporalType.DATE)
    private Calendar dateNaissance;

    @Temporal(TemporalType.TIMESTAMP)
    @Column(updatable=false)
    private Calendar dateCreation;

    @Lob
    @Basic(fetch=FetchType.LAZY)
    private byte[] image;

    // les getter/setter ont été omis pour faciliter la lecture
}
```

À l’entité JPA ci-dessus, on pourra faire correspondre la table MySQL :

```sql
CREATE TABLE `individu` (
  `individuId` int NOT NULL AUTO_INCREMENT,
  `nom` varchar(30) NOT NULL,
  `prenom` varchar(30) NOT NULL,
  `dateNaissance` DATE,
  `dateCreation` TIMESTAMP,
  `image` BLOB,
  PRIMARY KEY (`individuId`)
);
```

## L’EntityManager

Les annotations JPA que nous avons vues dans la section précédente, ne
servent à rien si elle ne sont pas exploitées programmatiquement. Dans
JPA, l’interface centrale qui va exploiter ces annotations est
l’interface [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html).

### Obtenir un EntityManager

JPA est une spécification. Pour pouvoir l’utiliser, il faut avoir à sa disposition
une implémentation compatible avec JPA. Dans le cadre de ce cours, nous utiliserons
[Hibernate](http://hibernate.org/). Pour ajouter Hibernate dans un projet Java, nous pouvons utiliser
[Maven](https://maven.apache.org/) pour gérer notre projet et ajouter comme dépendance dans le fichier
`pom.xml` :

```xml
<dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
        <version>5.1.6.Final</version>
</dependency>
```

Il faut fournir à l’implémentation de JPA un fichier XML de déploiement
nommé **persistence.xml**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
             http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd"
             version="2.1">
  <persistence-unit name="monUniteDePersistance">
    <!-- la liste des noms complets des classes représentant
         les entités gérées par cette unité de persistance  -->
    <class>ma.classe.Entite</class>
    <properties>
      <!-- une propriété de configuration propre à l'implémentation de JPA -->
      <property name="une propriété" value="une valeur" />
    </properties>
  </persistence-unit>
</persistence>
```

Dans ce fichier, on déclare une ou plusieurs unités de persistance grâce
à la balise `<persitence-unit>`. Chaque unité de persistance est
identifiée par un nom et contient la liste des classes entités gérées
par cette unité avec la balise `<class>`. La balise `<properties>`
permet de spécifier des propriétés propres à une implémentation de JPA et
que indique comment se connecter au SGBDR.

#### NOTE
La liste complète des paramètres de configuration propres à [Hibernate](http://hibernate.org/) est disponible dans la
[documentation officielle](http://docs.jboss.org/hibernate/orm/5.1/userguide/html_single/Hibernate_User_Guide.html#configurations).

Le fichier **persistence.xml doit se situer dans le répertoire
META-INF** et être disponible dans le classpath à l’exécution. Dans un
projet Maven, il suffit de créer ce fichier dans le répertoire
**src/main/resources/META-INF** du projet (créez les répertoires
manquants si nécessaire).

On ajoute ensuite dans le fichier **persistence.xml** les propriétés permettant
de décrire la connexion à la base de données.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
             http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd"
             version="2.1">
    <persistence-unit name="monUniteDePersistance">
            <properties>
                <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/database" />
                <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver" />
                <property name="javax.persistence.jdbc.user" value="root" />
                <property name="javax.persistence.jdbc.password" value="root" />
                <property name="hibernate.show_sql" value="true" />
                <property name="hibernate.format_sql" value="true" />
            </properties>
    </persistence-unit>
</persistence>
```

### Créer une fabrique d’EntityManager

Pour initialiser JPA, il faut utiliser la classe [Persistence](https://docs.oracle.com/javaee/7/api/javax/persistence/Persistence.html). Grâce à cette
classe, nous allons pouvoir créer une instance de [EntityManagerFactory](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManagerFactory.html).
Cette dernière, comme son nom l’indique, permet de fabriquer une instance d”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html).

```java
// on spécifie le nom de l'unité de persistence en paramètre
EntityManagerFactory emf = Persistence.createEntityManagerFactory("monUniteDePersistance");

EntityManager entityManager = emf.createEntityManager();
```

Il existe une méthode
`Persistence.createEntityManagerFactory(java.lang.String, java.util.Map properties)`
qui permet de spécifier des propriétés comme second paramètre. Ces
propriétés s’ajoutent ou remplacent celles déclarées dans la balise
`<properties>` du fichier *persistence.xml* pour l’unité de persistance.

Pour des raisons de performance, **une seule instance**
de la classe [EntityManagerFactory](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManagerFactory.html) devrait être créée par unité de persistance
et par application.

Par contre, une instance d”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) n’est pas prévue pour
être conservée trop longtemps. De plus, un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) n’est pas
conçu pour être utilisé dans un environnement concurrent. Pour des
applications multi-threadées, on utilisera une instance
d”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) par thread.

Un [EntityManagerFactory](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManagerFactory.html) et un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html)
représentent des ressources système et **doivent être fermées** par un
appel à leur méthode `close()` dès qu’elles ne sont plus utiles.

```java
EntityManager entityManager = emf.createEntityManager();
try {

  // ...

}
finally {
  entityManager.close();
}
```

Ni [EntityManagerFactory](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManagerFactory.html) ni [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) n’implémentent
l’interface `AutoCloseable`. Il n’est donc pas possible d’utiliser la
syntaxe du try-with-resources avec ces interfaces.

#### NOTE
Nous verrons par la suite que la procédure pour récupérer une instance d’un
[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) est différente si nous développons une application Java EE
destinée à être déployée dans un serveur d’application.

## Manipuler des entités à partir d’un EntityManager

À partir d’une instance d”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html), nous allons pouvoir
manipuler les entités afin de les créer, les modifier, les charger ou
les supprimer. Pour cela, nous disposons de six méthodes :

- find
- persist
- merge
- detach
- refresh
- remove

L”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) va prendre en charge la relation avec la base de
données et la génération des requêtes SQL nécessaires.

```java
EntityManager entityManager = ... // nous faisons l'hypothèse que nous disposons d'une instance
Individu individu = new Individu();
individu.setPrenom("John");
individu.setNom("Smith");

// Demande d'insertion dans la base de données
entityManager.persist(individu);

// Demande de chargement d'une entité.
// Le second paramètre correspond à la valeur de la clé de l'entité recherchée.
individu = entityManager.find(Individu.class, individu.getId());

// Demande de suppression (delete)
entityManager.remove(individu);
```

De plus, l’implémentation JPA se charge d’extraire ou au contraire de
positionner les attributs dans l’instance de l’entité. Par exemple, un
appel à `find` retourne bien une instance de la classe spécifiée par
le premier paramètre. Cette instance aura ses attributs renseignés à
partir des valeurs des colonnes sur lesquelles ils ont été mappés.

Pour les opérations qui modifient une entité (telles que `persist` ou
`remove`), il faut que l’appel se fasse dans le cadre d’une
transaction. Grâce à la méthode `EntityManager.getTransaction()`, il
est possible de récupérer la transaction est de gérer la démarcation
comme ci-dessous :

```java
EntityManager entityManager = ... // nous faisons l'hypothèse que nous disposons d'une instance

entityManager.getTransaction().begin();
boolean transactionOk = false;
try {
// ..

transactionOk = true;
}
finally {
    if(transactionOk) {
        entityManager.getTransaction().commit();
    }
    else {
        entityManager.getTransaction().rollback();
    }
}
```

#### NOTE
Nous verrons plus tard que l’exemple ci-dessus **ne fonctionne pas**
dans un serveur Java EE qui utilise l’API de gestion des transactions
JTA.

Attention cependant à ne pas croire que JPA est simplement un framework
pour générer du SQL. Une des difficultés dans la maîtrise de JPA
consiste justement à comprendre comment il gère le cycle de vie des
entités indépendamment de la base de données. Ainsi, on ne retrouve pas
sur l’interface [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) des noms de méthodes qui correspondent
aux instructions SQL `INSERT`, `SELECT`, `UPDATE` et `DELETE`.
Il ne s’agit pas d’un effet de style, les méthodes pour manipuler les
entités ont un comportement qui dépasse la simple exécution de requêtes
SQL.

Un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) cherche à limiter les interactions inutiles avec la
base de données. Ainsi, tant qu’une transaction est en cours, le moteur
JPA n’effectuera aucune requête SQL, à moins d’y être obligé pour
garantir l’intégrité des données. Il attendra si possible le commit de
la transaction. Ainsi si une entité est créée puis modifiée au cours de
la même transaction, plutôt que d’exécuter deux requêtes SQL (INSERT
puis UPDATE), l”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) attendra la fin de la transaction
pour réaliser une seule requête SQL (INSERT) avec les données
définitives.

### La méthode persist

La méthode `persist` ne se contente pas d’enregistrer une entité en
base, elle positionne également la valeur de l’attribut représentant la
clé de l’entité. La détermination de la valeur de la clé dépend de la
stratégie spécifiée par [@GeneratedValue](https://docs.oracle.com/javaee/7/api/javax/persistence/GeneratedValue.html).
L’insertion en base ne se fait pas nécessairement au moment de l’appel à
la méthode `persist` (on peut toutefois forcer l’insertion avec la
méthode `EntityManager.flush()`). Cependant, l”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html)
garantit que des appels successifs à sa méthode `find` permettront de
récupérer l’instance de l’entité.

C’est une erreur d’appeler la méthode `EntityManager.persist` en
passant une entité dont l’attribut représentant la clé est non null. La
méthode jette alors l’exception [EntityExistsException](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityExistsException.html).

### La méthode find

La méthode `EntityManager.find (Class<T>, Object)` permet de
rechercher une entité en donnant sa clé primaire. Un appel à cette
méthode ne déclenche pas forcément une requête `SELECT` vers la base
de données.

En effet, un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) agit également comme un cache au dessus
de la base de données. Ainsi, il garantit l’unicité des instances des
objets. Si la méthode `find` est appelée plusieurs fois sur la même
instance d’un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) avec une clé identique, alors l’instance
retournée est toujours **la même**.

```java
EntityManager entityManager = ... // nous faisons l'hypothèse que nous disposons d'une instance

Individu individu  = entityManager.find(Individu.class, 1);
// Pour le second appel à find, aucune requête SQL n'est exécutée.
// L'EntityManager se contente de retourner la même instance que précédemment.
Individu individu2 = entityManager.find(Individu.class, 1);

// individu == individu2
```

#### NOTE
Il existe également la méthode
`EntityManager.find(Class<T>, Object, LockModeType)`. Cette méthode
permet de récupérer une entité en posant un verrou. Elle est utilisée
pour réaliser un verrouillage optimiste ou pessimiste (appelé parfois
`select for update` en SQL).

### La méthode refresh

La méthode `EntityManager.refresh(Object)` annule toutes les
modifications faites sur l’entité durant la transaction courante et
recharge son état à partir des valeurs en base de données.

#### NOTE
Il existe également la méthode
`EntityManager.refresh(Class<T>, Object, LockModeType)`. Cette méthode
permet de rafraîchir une entité en vérouillant l’accès en écriture. Elle
est utilisée pour réaliser un verrouillage optimiste ou pessimiste
(appelé parfois `select for update` en SQL).

### La méthode merge

La méthode `EntityManager.merge(T)` est parfois considérée comme la
méthode permettant de réaliser les `UPDATE` des entités en base de
données. Il n’en est rien et la sémantique de la méthode `merge` est
très différente. En fait, il **n’existe pas** à proprement parlé de
méthode pour réaliser la mise à jour d’une entité. Un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html)
surveille les entités dont il a la charge et réalise les mises à jour si
nécessaire au commit de la transaction. Par exemple le code ci-dessous
suffit à déclencher une requête SQL UPDATE :

```java
EntityManager entityManager = ... // nous faisons l'hypothèse que nous disposons d'une instance

entityManager.getTransaction().begin();
try {
    Individu individu = entityManager.find(Individu.class, 1L);
    if (individu != null) {
        individu.setPrenom("Vincent");
    }
    // Si le prénom a été modifié, JPA est
    // capable de le détecter et de déclencher un UPDATE
    // au moment du commit.
    entityManager.getTransaction().commit();
}
catch (RuntimeException e) {
    entityManager.getTransaction().rollback();
    throw e;
}
```

Si un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) détecte automatiquement les modifications des
entités dont il a la charge, à quoi peut donc servir la méthode
`EntityManager.merge(T)` ? En fait si vous créez vous même une
instance d’une entité et que vous positionnez la clé, cette entité n’est
gérée par aucun [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html). Pour qu’un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) prenne
en compte votre entité, il faut appeler la méthode `merge` :

```java
EntityManager entityManager = ... // nous faisons l'hypothèse que nous disposons d'une instance

entityManager.getTransaction().begin();

Individu individu = new Individu();
// on positionne explicitement l'id de l'entité
individu.setId(1L);

try {
    // il est très important de remplacer notre instance
    // par celle retournée par l'EntityManager après un merge.
    individu = entityManager.merge(individu);

    // on rafraîchit les données de la nouvelle entité
    entityManager.refresh(individu);

    // l'instance de individu contient bien le prénom stocké en base
    // de données (l'appel à merge à récupérer l'information)
    individu.setPrenom("Vincent");

    // JPA est capable de détecter que l'age de l'individu a été modifié
    // et qu'il faut réaliser un UPDATE SQL au moment du commit.
    entityManager.getTransaction().commit();
}
catch (RuntimeException e) {
    entityManager.getTransaction().rollback();
    throw e;
}
```

L’inverse de la méthode `EntityManager.merge(T)` est
`EntityManager.detach(Object)` qui annule la gestion d’une entité par
l”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html).

### La méthode detach

Comme son nom l’indique, la méthode `EntityManager.detach(Object)`
détache une entité, c’est-à-dire que l’instance passée en paramètre ne
sera plus gérée par l”[EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html). Ainsi, lors du commit de la
transaction, les modifications faites sur l’entité détachée ne seront
pas prises en compte.

### La méthode remove

La méthode `EntityManager.remove(Object)` supprime une entité. Si
l’entité a déjà été persistée en base de données, cette méthode
entraînera une requête SQL DELETE.
