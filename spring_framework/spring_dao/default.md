# Spring DAO

DAO (*Data Access Object*) est une responsabilité qui est souvent utilisée dans
les applications d’entreprise. Dans le code source d’une application, on peut
trouver des classes nommées `UserDao`, `ProducDao`… Ce suffixe « Dao » dénote
que la classe a pour responsabilité d’accéder au système d’information pour lire
ou modifier des données. Comme la plupart des applications d’entreprise stockent
leurs données dans une base de données, les classes DAO sont donc les classes
qui contiennent le code qui permet d’échanger des informations avec la base de données.
En Java, selon la technologie utilisée, il peut s’agir des classes qui utilisent
l’API JDBC ou JPA par exemple.

Le module *Spring Data Access* reprend ce principe d’architecture en cherchant à simplifier l’intégration
et l’implémentation des interactions avec les bases de données.

## L’annotation @Repository

Le Spring Framework fournit des stéréotypes pour marquer le rôle des classes.
Le stéréotype le plus général est défini par l’annotation [@Component](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html).
Il est également possible d’utiliser l’annotation [@Repository](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html) pour indiquer qu’une
classe sert de point d’accès à un mécanisme de stockage et de recherche d’une
collection d’objets. La notion de *repository* vient de l’ouvrage de Eric
Evans (*Domain Driven Development*).

```java
{% if not jupyter %}
```

> package {{ROOT_PKG}};

{% endif %}

> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > public void save(User user) {
> > : // …

> > }

> > public User getById(long id) {
> > : // …

> > }

> }

## Intégration de JPA

Pour une application utilisant JPA et qui accepte la
[configuration par annotations](annotations.md#spring-configuration-annotations), il est
possible d’injecter un [EntityManager](https://docs.oracle.com/javaee/7/api/javax/persistence/EntityManager.html) dans un *repository* grâce à l’annotation
[@Autowired](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Autowired.html), [@Inject](https://docs.oracle.com/javaee/7/api/javax/inject/Inject.html) et même [@PersistenceContext](https://docs.oracle.com/javaee/7/api/javax/persistence/PersistenceContext.html) (qui est l’annotation
standard de Java EE).

```java
```

{% if not jupyter %}
: package {{ROOT_PKG}};

{% endif %}

> import javax.persistence.EntityManager;
> import javax.persistence.PersistenceContext;

> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > @PersistenceContext
> > private EntityManager entityManager;

> > public void save(User user) {
> > : // …

> > }

> > public User getById(long id) {
> > : // …

> > }

> }

#### NOTE
Pour activer JPA, il faut configurer le contexte d’application avec
[un gestionnaire de transaction JPA](spring_tx.md#spring-tx-transaction-jpa).

## Uniformité de la hiérarchie des exceptions

Un apport du module *Spring Data Access* est d’uniformiser la hiérarchie des exceptions.
En effet, l’API JDBC utilise des exceptions héritant
de [SQLException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/SQLException.html) qui est une *checked* exception. JPA utilise des *unechecked*
exceptions héritant de [PersistenceException](https://docs.oracle.com/javaee/7/api/javax/persistence/PersistenceException.html). D’autres bibliothèques ou
*frameworks* proposent à leur tour leur propre hiérarchie d’exceptions.

Pour simplifier la gestion des exceptions, *Spring Data Access* propose une
hiérarchie unique d’exceptions pour toutes ces technologies afin de simplifier
la gestion des erreurs pour les applications.

![Hiérarchie des exceptions](spring_framework/assets/spring_data_exceptions.png)

À la base de cette hiérarchie, la classe [DataAccessException](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/dao/DataAccessException.html) est une *unchecked*
exception (elle hérite de [RuntimeException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/RuntimeException.html)).

Pour une application utilisant JPA, l’uniformisation de la hiérarchie des exceptions
n’est pas activée par défaut. Pour l’activer, il faut utiliser l’annotation
[@Repository](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html) et déclarer dans le contexte d’application un *bean* de type
[PersistenceExceptionTranslationPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/dao/annotation/PersistenceExceptionTranslationPostProcessor.html).

```xml
<bean class="org.springframework.dao.annotation.PersistenceExceptionTranslationPostProcessor" />
```

## Accès aux données avec JDBC

*Spring Data Access* fournit la classe [JdbcTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html) pour encapsuler les appels
JDBC. Cette classe est simplement une classe utilitaire qui réalise :

* la traduction d’une éventuelle [SQLException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/SQLException.html) dans la hiérarchie uniformisée des
  exceptions de *Spring Data Access*
* l’encapsulation des appels à [Statement](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/Statement.html) et [PreparedStatement](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/PreparedStatement.html)
* une aide pour la création d’objets à partir d’un [ResultSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/ResultSet.html)

#### NOTE
Pour activer ce support avancé de JDBC, il faut ajouter comme dépendance Maven :

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.0.7.RELEASE</version>
</dependency>
```

La classe [JdbcTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html) se construit à partir d’une [DataSource](https://docs.oracle.com/javase/8/docs/api/index.html?javax/sql/DataSource.html). L’implémentation
recommandée est de créer une instance de [JdbcTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html) au moment de l’injection
de la [DataSource](https://docs.oracle.com/javase/8/docs/api/index.html?javax/sql/DataSource.html) dans le *bean*. Ainsi, il est très simple de définir la [DataSource](https://docs.oracle.com/javase/8/docs/api/index.html?javax/sql/DataSource.html)
dans le contexte de déploiement *Spring* de l’application (par JNDI, en utilisation un gestionnaire
de connexions comme [DBCP](https://commons.apache.org/proper/commons-dbcp/apidocs/index.html)).

```java
```

{% if not jupyter %}
: package {{ROOT_PKG}};

{% endif %}

> import javax.sql.DataSource;

> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.jdbc.core.JdbcTemplate;
> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > private JdbcTemplate jdbcTemplate;

> > @Autowired
> > public void setDataSource(DataSource dataSource) {

> > > this.jdbcTemplate = new JdbcTemplate(dataSource);

> > }

> > // …

> }

#### NOTE
L’implémentation de la classe [JdbcTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html) est *thread-safe*. Cela signifie qu’elle
peut être déclarée comme attribut d’un *bean singleton* utilisé dans un environnement
concurrent (comme dans un serveur).

La classe [JdbcTemplate](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/JdbcTemplate.html) permet d’exécuter des requêtes SQL de manière simplifiée.

```java
```

{% if not jupyter %}
: package {{ROOT_PKG}};

{% endif %}

> import java.sql.ResultSet;
> import java.sql.SQLException;
> import java.util.List;

> import javax.sql.DataSource;

> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.jdbc.core.JdbcTemplate;
> import org.springframework.jdbc.core.RowMapper;
> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > private JdbcTemplate jdbcTemplate;

> > @Autowired
> > public void setDataSource(DataSource dataSource) {

> > > this.jdbcTemplate = new JdbcTemplate(dataSource);

> > }

> > public int getUserCount() {
> > : return jdbcTemplate.queryForObject(« select count(1) from User », Integer.class);

> > }

> > public User getUserById(long id) {
> > : return jdbcTemplate.queryForObject(« select \* from User where id = ? »,
> >   : new Object[] {id}, new UserRowMapper());

> > }

> > public List<User> getAll() {
> > : return jdbcTemplate.query(« select \* from User », new UserRowMapper());

> > }

> > private final class UserRowMapper implements RowMapper<User> {
> > : @Override
> >   public User mapRow(ResultSet rs, int rowNum) throws SQLException {
> >   <br/>
> >   > User user = new User();
> >   > user.setId(rs.getLong(« id »));
> >   > user.setNom(rs.getString(« nom »));
> >   > return user;
> >   <br/>
> >   }

> > }

> }

Dans l’exemple ci-dessus, la classe interne `UserRowMapper` implémente
l’interface [RowMapper<T>](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/RowMapper.html) qui permet de transformer une ligne retournée par
un [ResultSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/sql/ResultSet.html) en objet.

Spring Data Access fournit également la classe utilitaire [SimpleJdbcInsert](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/simple/SimpleJdbcInsert.html)
pour faciliter la génération de requête d’insertion :

```java
```

{% if not jupyter %}
: package {{ROOT_PKG}};

{% endif %}

> import java.util.HashMap;
> import java.util.Map;

> import javax.sql.DataSource;

> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.jdbc.core.JdbcTemplate;
> import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > private JdbcTemplate jdbcTemplate;
> > private SimpleJdbcInsert simpleJdbcInsert;

> > @Autowired
> > public void setDataSource(DataSource dataSource) {

> > > this.jdbcTemplate = new JdbcTemplate(dataSource);
> > > this.simpleJdbcInsert = new SimpleJdbcInsert(dataSource).withTableName(« User »);

> > }

> > public void save(User user) {
> > : Map<String,Object> params = new HashMap<String, Object>();
> >   params.put(« name », user.getName());
> >   <br/>
> >   simpleJdbcInsert.execute(params);

> > }

> > // …

> }

À la ligne 22, on crée une instance de [SimpleJdbcInsert](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/core/simple/SimpleJdbcInsert.html) en précisant le nom
de la table pour laquelle on souhaite générer des requêtes d’insertion. Aux
lignes 26-27, on crée un dictionnaire des valeurs à insérer et enfin, à la
ligne 29, on appelle la méthode `execute` en passant le dictionnaire
des paramètres. La méthode génère et exécute la requête SQL d’insertion.

Avec cette classe utilitaire, il est même possible de récupérer la clé
primaire générée (pour le cas d’une colonne *auto increment* avec MySQL
par exemple) :

```java
```

{% if not jupyter %}
: package {{ROOT_PKG}};

{% endif %}

> import java.util.HashMap;
> import java.util.Map;

> import javax.sql.DataSource;

> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.jdbc.core.JdbcTemplate;
> import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
> import org.springframework.stereotype.Repository;

> @Repository
> public class UserDao {

> > private JdbcTemplate jdbcTemplate;
> > private SimpleJdbcInsert simpleJdbcInsert;

> > @Autowired
> > public void setDataSource(DataSource dataSource) {

> > > this.jdbcTemplate = new JdbcTemplate(dataSource);
> > > this.simpleJdbcInsert = new SimpleJdbcInsert(dataSource).withTableName(« User »)

> > > > .usingGeneratedKeyColumns(« id »);

> > }

> > public void save(User user) {
> > : Map<String,Object> params = new HashMap<String, Object>();
> >   params.put(« name », user.getName());
> >   <br/>
> >   Number key = simpleJdbcInsert.executeAndReturnKey(params);
> >   user.setId(key.longValue());

> > }

> > // …

> }

Dans l’exemple ci-dessus, on précise à la ligne 23 la colonne correspondant à
la clé primaire. Puis, à la ligne 30, on appelle la méthode `executeAndReturnKey`
afin d’insérer les données et de récupérer la clé primaire du nouvel enregistrement
pour pouvoir la positionner dans l’objet de type `User`.
