# JDBC dans une application Web

JDBC (Java DataBase Connectivity) est l’API standard pour interagir avec les
bases données relationnelles en Java. Cette API peut être utilisée dans une
application Web.

<a id="datasource-ref"></a>

## Déclaration d’une DataSource

Dans un serveur d’application, l’utilisation du [DriverManager](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/DriverManager.html) JDBC est remplacée
par celle de la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html). L’interface [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) n’offre que deux méthodes :

```java
// Attempts to establish a connection with the data source
Connection getConnection()

// Attempts to establish a connection with the data source
Connection getConnection(String username, String password)
```

Il n’est pas possible de spécifier l’URL de connection à la base de données
avec une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html). Par contre une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) peut être injectée dans
n’importe quel composant Java EE grâce à l’annotation [Resource](https://docs.oracle.com/javaee/7/api/javax/annotation/Resource.html) :

```java
import java.io.IOException;
import java.sql.Connection;

import javax.annotation.Resource;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.sql.DataSource;

@WebServlet("/MyServlet")
public class MyServlet extends HttpServlet {

  @Resource(name = "nomDeLaDataSource")
  private DataSource dataSource;

  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp)
                          throws ServletException, IOException {

    try (Connection connection = dataSource.getConnection()) {
      // ...
    }

  }

}
```

L’annotation [Resource](https://docs.oracle.com/javaee/7/api/javax/annotation/Resource.html) permet de spécifier le nom de la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) grâce à
l’attribut *name*.

#### NOTE
L’annotation [Resource](https://docs.oracle.com/javaee/7/api/javax/annotation/Resource.html) se base sur **JNDI** (Java Naming and Directory
Interface) pour rechercher la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) demandée. JNDI est une API standard
de Java permettant de se connecter à des annuaires (notamment les annuaires
LDAP). Les serveurs d’application Java EE disposent de leur propre implémentation
interne d’annuaire permettant de stocker des instances d’objet.

Les ressources telles que les *DataSources* sont donc stockées dans un annuaire
interne et il est possible d’y accéder avec l’API JNDI. Les ressources sont
classées dans une arborescence (comme le sont les fichiers dans un système
de fichiers). Une ressource est stockée dans l’arborescence **java:/comp/env**.

```java
// javax.naming.InitialContext désigne le contexte racine de l'annuaire.
// Un annuaire JDNI est constitué d'instances de javax.naming.Context
// (qui sont l'équivalent des répertoires dans un système de fichiers).
Context envContext = InitialContext.doLookup("java:/comp/env");

// On récupère la source de données dans le contexte java:/comp/env
DataSource dataSource = DataSource.class.cast(envContext.lookup("nomDeLaDataSource"));
```

Le contexte JNDI **java:/comp/env** est un contexte particulier. Il désigne
l’ensemble des composants Java EE disponibles dans l’environnement (env) du
composant Java EE (comp) courant.

<a id="configuration-datasource"></a>

## Déclaration de la DataSource dans le fichier web.xml

Le fichier de déploiement `web.xml` doit déclarer la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) comme
une ressource de l’application. Cela va permettre au serveur d’application
de permettre à l’application de se connecter à la base de données associée.
Pour cela, on utilise l’élément `<resource-ref>` dans le fichier `web.xml` :

```xml
<resource-ref>
<res-ref-name>nomDeLaDataSource</res-ref-name>
  <res-type>javax.sql.DataSource</res-type>
  <mapped-name>java:/nomDeLaDataSource</mapped-name>
</resource-ref>
```

Mais comment le serveur d’application fait-il pour lier
une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) avec une connexion vers une base de données ? Malheureusement,
il n’existe pas de standard et chaque serveur d’application dispose de sa
procédure. Nous allons voir dans la section suivante comment créer une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html)
spécifiquement pour Wildfly.

## Déclaration d’une DataSource dans Wildfly

Une connexion JDBC est réalisée à travers un pilote. Pour déclarer une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html)
vers une base de données MySQL par exemple, nous devons installer le pilote
MySQL dans le serveur.

Ce système de configuration est certes plus compliqué que l’utilisation du
[DriverManager](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/sql/DriverManager.html) mais il permet à l’application d’ignorer les détails de
configuration. Généralement le développeur de l’application référence une [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html)
et c’est l’administrateur du serveur qui configure la connexion de cette [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html)
vers une base de données spécifiques.

L’utilisation des *DataSources* dans un serveur d’application
apporte également des fonctionnalités supplémentaires telles que la mise en
cache et la réutilisation de connexions (pour améliorer les performances),
les tests permettant de vérifier que les connexions sont correctement
établies, la supervision des connexions…

Pour ajouter ce pilote dans Wildfly, nous pouvons :

* soit laisser le serveur le faire grâce à une procédure de déploiement
  automatisé.
* soit ajouter un nouveau module dans le serveur

Les sections suivantes décrivent respectivement ces méthodes. La première méthode
est assez simple à réaliser et elle est donc recommandée pour commencer.
La seconde, plus complexe, donne plus d’options de configuration à l’administrateur du serveur.

### Déploiement automatique du driver MySQL (méthode 1)

#### NOTE
Vous pouvez télécharger le driver MySQL pour JDBC
[ici selon la version](http://mvnrepository.com/artifact/mysql/mysql-connector-java).

Pour déployer automatiquement le pilote MySQL dans Wildfly, il suffit de placer
le fichier *jar* du pilote dans le répertoire `standalone/deployments/`
depuis le répertoire d’installation du serveur.

Démarrez ensuite votre serveur et scrutez dans les logs de démarrage du serveur
la trace qui indique le déploiement par le serveur. Pour MySQL, vous devriez avoir
une ligne de log telle que :

```text
09:51:40,222 INFO  [org.jboss.as.connector.deployers.jdbc] (MSC service thread 1-4) WFLYJCA0018: Started Driver service with driver-name = mysql-connector-java-5.1.46.jar_com.mysql.jdbc.Driver_5_1
```

Cette ligne (un peu longue) indique tout au bout le nom attribué automatiquement
au driver par le serveur au démarrage. Pour l’exemple ci-dessus, le nom est
**mysql-connector-java-5.1.46.jar_com.mysql.jdbc.Driver_5_1**. Mais attention
ce nom peut être différent pour votre configuration.

#### NOTE
Remarquez que le répertoire `standalone/deployments/` dans lequel vous
avez placé le driver de base de données contient également les fichiers *war*
des applications que vous avez déjà déployées. Ce répertoire est scruté en
permanence par le serveur et il déploie les modules qui sont copiés dedans.

Une fois, le pilote déployé automatiquement par le serveur, il est possible
de déclarer la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) dans le fichier `standalone/configuration/standalone.xml`.
Au alentour de la ligne 140 dans ce fichier, on trouve la section de déclaration
des pilotes de base de données et des sources de données :

```xml
  <subsystem xmlns="urn:jboss:domain:datasources:5.0">
      <datasources>
          <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
              <connection-url>jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE</connection-url>
              <driver>h2</driver>
              <security>
                  <user-name>sa</user-name>
                  <password>sa</password>
              </security>
          </datasource>
          <datasource jta="true" jndi-name="java:/nomDeLaDataSource" pool-name="nomDeLaDataSource" enabled="true">
              <connection-url>jdbc:mysql://[HOST]:[PORT]/[NOM SCHEMA]</connection-url>
              <driver>mysql-connector-java-5.1.46.jar_com.mysql.jdbc.Driver_5_1</driver>
              <security>
                  <user-name>[LOGIN]</user-name>
                  <password>[PASSWORD]</password>
              </security>
          </datasource>
          <drivers>
              <driver name="h2" module="com.h2database.h2">
                  <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
              </driver>
          </drivers>
      </datasources>
  </subsystem>
```

À la ligne 153, on indique le driver de base de données à utiliser en donnant
le nom du driver déployé automatiquement par le serveur.

### Ajout du driver MySQL comme nouveau module (méthode 2)

#### NOTE
Vous pouvez télécharger le driver MySQL pour JDBC
[ici selon la version](http://mvnrepository.com/artifact/mysql/mysql-connector-java).

Pour ajouter le pilote MySQL dans Wildfly, nous pouvons créer un nouveau module dans le serveur.
Pour cela, à partir du répertoire d’installation du serveur lui-même, placez
le fichier *jar* du pilote dans le répertoire `modules/system/layers/base/com/mysql/driver/main`.
Créez les répertoires manquants si nécessaire.

Créez ensuite le fichier `module.xml` dans le répertoire
`modules/system/layers/base/com/mysql/driver/main`. Ce fichier doit pointer
sur le fichier *jar* du pilote :

```xml
<?xml version='1.0' encoding='UTF-8'?>
<module xmlns="urn:jboss:module:1.5" name="com.mysql.driver">
  <resources>
    <!-- Indiquez le chemin vers le fichier jar du pilote -->
    <resource-root path="mysql-connector-java-X.X.X.jar" />
  </resources>
  <dependencies>
    <module name="javax.api"/>
    <module name="javax.transaction.api"/>
    <module name="javax.servlet.api" optional="true"/>
    <module name="javax.ws.rs.api" optional="true"/>
  </dependencies>
</module>
```

Une fois, le pilote déclaré comme un module dans le serveur, il est possible
de déclarer la [DataSource](https://docs.oracle.com/javase/8/docs/api/javax/sql/DataSource.html) dans le fichier `standalone/configuration/standalone.xml`.
Au alentour de la ligne 140 dans ce fichier, on trouve la section de déclaration
des pilotes de base de données et des sources de données :

```xml
  <subsystem xmlns="urn:jboss:domain:datasources:5.0">
      <datasources>
          <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
              <connection-url>jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE</connection-url>
              <driver>h2</driver>
              <security>
                  <user-name>sa</user-name>
                  <password>sa</password>
              </security>
          </datasource>
          <datasource jta="true" jndi-name="java:/nomDeLaDataSource" pool-name="nomDeLaDataSource" enabled="true">
              <connection-url>jdbc:mysql://[HOST]:[PORT]/[NOM SCHEMA]</connection-url>
              <driver>mysql</driver>
              <security>
                  <user-name>[LOGIN]</user-name>
                  <password>[PASSWORD]</password>
              </security>
          </datasource>
          <drivers>
              <driver name="h2" module="com.h2database.h2">
                  <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
              </driver>
              <driver name="mysql" module="com.mysql.driver">
                  <driver-class>com.mysql.jdbc.Driver</driver-class>
              </driver>
          </drivers>
      </datasources>
  </subsystem>
```
