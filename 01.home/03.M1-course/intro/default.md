# Apache Camel Introduction

Le projet open source Apache Camel est à l’avant-garde de l’adoption généralisée des modèles d’intégration d’entreprise sur la JVM depuis de nombreuses années. En fait, il a été si populaire que certains développeurs d’autres langages de programmation populaires l’ont cité comme une forte influence lorsqu’ils ont mis en œuvre des projets similaires. L’approche simple, intuitive et extensible d’Apache Camel a permis même aux développeurs novices de produire des solutions fiables à des problèmes complexes dans un délai réaliste.

## Intro

Apache Camel est un framework d’intégration open source qui vise à faciliter l’intégration des systèmes. Dans cette section, nous vous présenterons Camel et vous montrerons comment il s’intègre dans le paysage des logiciels d’entreprise. Vous apprendrez également les concepts et la terminologie de Camel.

Au cœur du framework Camel se trouve un moteur de routage, ou plus précisément, un constructeur de moteur de routage. Il vous permet de définir vos propres règles de routage, de décider à partir de quelles sources accepter les messages et de déterminer comment traiter et envoyer ces messages vers d’autres destinations. Camel utilise un langage d’intégration qui vous permet de définir des règles de routage complexes, semblables à des processus métier. Comme le montre la figure suivante, Camel est la glue entre des systèmes disparates.

![](apache-camel/img/glue.png)

Camel propose des abstractions de haut niveau qui vous permettent d’interagir avec différents systèmes en utilisant la même API, quel que soit le protocole ou le type de données que les systèmes utilisent. Les composants de Camel fournissent des implémentations spécifiques de l’API qui ciblent différents protocoles et types de données. Prêt à l’emploi, Camel prend en charge plus de 280 protocoles et types de données. Son architecture extensible et modulaire vous permet d’implémenter et de brancher en toute transparence le support de vos propres protocoles, propriétaires ou non. Ces choix architecturaux éliminent le besoin de conversions inutiles et rendent Camel non seulement plus rapide, mais également plus léger. En conséquence, il convient à l’intégration dans d’autres projets qui nécessitent les riches capacités de traitement de Camel. D’autres projets open source, tels qu’Apache ServiceMix, Karaf et ActiveMQ, utilisent déjà Camel moteur d’intégration

## Langage spécifique au domaine (DSL)

À sa création, le langage spécifique au domaine (DSL) de Camel a été une contribution majeure aux mécanismes d’intégration. Depuis lors, plusieurs autres frameworks d’intégration ont emboité le pas et proposent désormais des DSL en Java, XML ou des langages personnalisés. L’objectif du DSL est de permettre au développeur de se concentrer sur le problème d’intégration plutôt que sur l’outil, le langage de programmation. Dans ce cours, nous exposerons uniquement le DSL Java d’Apache Camel, mais d’autres DSL existent (XML, Rest, Annotations, Yaml).

```java
    from("file:data/inbox").to("jms:queue:order");
```

cette configuration montre comment router des messages contenus dans un répertoire local nommé `data/inbox` vers que queue de message jms nomée `order`.

## Routeur indépendant de la payload

Camel peut acheminer n’importe quelle payload; vous n’êtes pas limité à transporter un format normalisé tel que les charges utiles XML. Cette liberté signifie que vous n’avez pas à transformer votre payload dans un format canonique pour faciliter le routage.

## Architecture modulaire et plug-and-play

Camel a une architecture modulaire, qui permet de charger n’importe quel composant dans Camel, qu’il soit livré avec Camel, qu’il provienne d’un tiers ou qu’il s’agisse de votre propre création personnalisée. Vous pouvez également configurer presque tout dans Camel. La plupart de ses fonctionnalités sont plug-and-play et configurables, qu’il s’agisse de la génération d’ID, de la gestion des threads, du séquenceur d’arrêt, de la mise en cache de flux, etc.

## modèle POJO

Les beans Java (ou Plain Old Java Objects, POJO) sont considérés comme des citoyens de première classe dans Camel, et Camel s’efforce de vous permettre d’utiliser des beans n’importe où et n’importe quand dans vos projets d’intégration. Dans de nombreux endroits, vous pouvez étendre les fonctionnalités intégrées de Camel avec votre propre code personnalisé.

## Configuration facile

La convention sur le paradigme de configuration est suivie chaque fois que possible, ce qui minimise les exigences de configuration. Afin de configurer les endpoints directement dans les routes, Camel utilise une configuration d’URI simple et intuitive.

Par exemple, vous pouvez configurer une route Camel à partir d’un point de terminaison de fichier pour analyser de manière récursive dans un sous-dossier et inclure uniquement les fichiers .txt, comme suit :

`java from("file:data/inbox?recursive=true&include=.*txt$") `

## Convertisseurs de types automatiques

Camel a un mécanisme de convertisseur de type intégré qui est livré avec plus de 350 convertisseurs. Vous n’avez plus besoin de configurer des règles de conversion de type pour passer des tableaux d’octets aux chaines, par exemple. Et si vous devez convertir vers des types que Camel ne prend pas en charge, vous pouvez créer votre propre convertisseur de type.

Les composants Camel utilisent également cette fonctionnalité ; ils peuvent accepter des données dans la plupart des types et convertir les données en un type qu’ils sont capables d’utiliser.

## Noyau léger idéal pour les microservices

Le noyau de Camel peut être considéré comme léger, la bibliothèque totale atteignant environ 4,9 Mo et n’ayant que 1,3 Mo de dépendances d’exécution. Cela rend Camel facile à intégrer ou à déployer où vous le souhaitez, comme dans une application autonome, un microservice, une application Web, une application Spring, une application Java EE, OSGi, Spring Boot, WildFly et dans des plateformes cloud telles que AWS, Kubernetes et Cloud Fonderie. Camel n’a pas été conçu pour être un serveur ou un ESB, mais plutôt pour être intégré à l’environnement d’exécution de votre choix. Vous avez juste besoin de Java.

Pour démarrer votre première application apache Apache Camel, rendez-vous à la section [Bien Démarrer](getting-started/)
