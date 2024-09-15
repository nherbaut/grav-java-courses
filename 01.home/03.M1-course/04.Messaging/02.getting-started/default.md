# Bien démarrer

## Prérequis

* [Quarkus CLI](https://quarkus.io/guides/cli-tooling)
* [intelliJ Idea](https://www.jetbrains.com/idea/)
* [Java JDK 21](https://www.oracle.com/java/technologies/java-se-glance.html)

## Initialisation du projet

Nous allons tout d’abord initialiser notre projet à l’aide de la CLI quarkus. Créez un nouveau répertoire et tapez la commande suivante:

```bash
quarkus create app fr.pantheonsorbonne.ufr27.miage:camel-01
```

Cette commande va créer votre projet Quarkus. Packagez ensuite le projet pour récupérer automatiquement les dépendances avec maven

```bash
cd camel-01
./mvnw package -DskipTests=true
```

## Ajout des dépendances Apache Camel

Nous alons maintenant ajouter les dépendances vers camel dont nous avons besoin. Tradictionnellement, les dépendances sont ajoutées dans le fichier `pom.xml`. L’utilisation de Quarkus facilite cette opération, en proposant d’ajouter automatiquement les bonnes dépendances depuis la ligne de commande, depuis le répertoire `camel-01`

```bash
quarkus ext add camel-quarkus-file
```

Ici, nous ajoutons à notre projet la possibilité d’utiliser jms, camel, camel-file et camel-jaxb.

## Ajout d’une classe CamalTutorial pour travailler sur les fichiers

Dans votre IDE, ajoutez une nouveau package `fr.pantheonsorbonne.ufr27.miage.camel`. Ajoutez-y une nouvelle classe `CamelTutoral`.

![](apache-camel/img/ide_new_package.png)

Ajouter ensuite le code java suivant:

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import org.apache.camel.builder.RouteBuilder;

import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CamelTutorial extends RouteBuilder {

    @Override
    public void configure() throws Exception {


        from("file:data/inbox").to("file:data/outbox");

    }
}
```

puis lancer votre programme quarkus

* à l’aide de la ligne de commande `quarkus dev`
* OU à l’aide de votre IDE

![](apache-camel/img/idea-quarkus.png)

Maintenant, lorsque vous créez des fichiers dans le répertoire `data/inbox`, ceux-ci sont automatiquement copié dans le répertoire `data/outbox`

![](apache-camel/img/demo-camel01.png)

La classe [RouteBuilder](https://camel.apache.org/manual/latest/route-builder.html) est la classe de base permettant de configurer les routes de Camel, pour cela, il faut réécrire la méthode `void configure()` et utiliser la DSL Java avec les méthodes `from` et `to`.
Remarquez que la première partie de la définition des route (`file:`) indique que le composant file est utilisé pour configurer la route. Le reste de la configuration étant spécifique à chaque composant

## Se connecter à un broker compatible JMS

Dans cette section, nous allons connecter notre class `CamelTutorial` à un broker JMS et faire transiter les messages entre `data/inbox` et `data/outbox` par le broker.
Camel supporte de nombreux broquer de messages, dans le reste de ce turotiel, nous allons utiliser Apache ActiveMQ Artemis, qui est l’implémentation de JMS utilisée par des containers d’applications comme JBOSS EAP et Wildfly.

### Installer les nouvelles librairies

Utilisez la commande `quarkus` ou modifiez manuellement le `pom.xml` pour installer les 2 nouvelles librairies permettant d’utiliser:

1. le composant camel “sjms2”
2. ActiveMQ Artemis avec camel

#### NOTE
La ligne de commande quarkus permet d’afficher toutes les extensions disponibles en filtrant par mot clé. Par exemple, la commande suivante:

```bash
quarkus ext list --concise -i -s jdbc
```

vous permet d’afficher la liste des pilotes jdbc disponible avec quarkus, ainsi que d’autres extensions aditionnnelles.

```java
Camel JDBC                                         camel-quarkus-jdbc                                
Camel SQL                                          camel-quarkus-sql                                 
Agroal - Database connection pool                  quarkus-agroal                                    
Elytron Security JDBC                              quarkus-elytron-security-jdbc                     
JDBC Driver - DB2                                  quarkus-jdbc-db2                                  
JDBC Driver - Derby                                quarkus-jdbc-derby                                
JDBC Driver - H2                                   quarkus-jdbc-h2                                   
JDBC Driver - MariaDB                              quarkus-jdbc-mariadb                              
JDBC Driver - Microsoft SQL Server                 quarkus-jdbc-mssql                                
JDBC Driver - MySQL                                quarkus-jdbc-mysql                                
JDBC Driver - Oracle                               quarkus-jdbc-oracle                               
JDBC Driver - PostgreSQL                           quarkus-jdbc-postgresql 
```

 <details>
   <summary>solution</summary>
   <pre>quarkus ext add camel-quarkus-sjms2 quarkus-artemis-jms</pre>
</details>

### Configurer Artemis

La configuration d’artemis se fait à l’aide d’un fichier de configuration présent dans le projet camel-01 dans le fichier `src/main/resources/application.properties`.

Ajoutez les lignes suivantes au fichier:

```properties
quarkus.artemis.url=
quarkus.artemis.username=
quarkus.artemis.password=
```

utilisez le serveur, username et le password fournis par l’enseignant.

### Configurer les nouvelles routes

Vous pouvez configurer une route vers JMS en utilisant le code Java suivant:

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import org.apache.camel.builder.RouteBuilder;

import javax.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CamelTutorial extends RouteBuilder {

    @Override
    public void configure() throws Exception {
          from("file:data/inbox").to("sjms2:M1.fileexchange");
        from("sjms2:M1.fileexchange").to("file:data/outbox");
    }
}

```

Ici, nous définissions 2 routes: celle qui consomme les fichiers dans le répertoire `data/inbox` puis les envoies, avec JMS, dans la queue `M1.fileexchange` et une autre route qui lit les messages publiés dans la queue `M1.fileexchange` et les écrit sous forme de fichier dans `data/outbox`. Le résultat est identique

#### NOTE
![Les URI des endpoints sont divisée en 3 parties: un schema (scheme), un chemin (context path) et des options](apache-camel/img/endpoint.png)

Le schéma (1) indique quel composant Camel gère ce type de endpoint. Dans ce cas, le schéma de fichier sélectionne FileComponent. FileComponent fonctionne alors comme une factory, créant FileEndpoint en fonction des parties restantes de l’URI. Le chemin de contexte `data/inbox` (2) indique à FileComponent que le dossier de départ est data/inbox. L’option, delay=5000 (3) indique que les fichiers doivent être interrogés à un intervalle de 5 secondes.
