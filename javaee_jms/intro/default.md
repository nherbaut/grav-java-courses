# Introduction

L’API de message de JAVA fournit un mécanisme d’envoi de message entre deux application JavaEE.
La (version 2.0 de la spécification)[[https://jcp.org/en/jsr/detail?id=343](https://jcp.org/en/jsr/detail?id=343)] simplifie grandement le développement des fonctionnalités d’envoi de message.

Les application JMS ne communiquement pas directement, au contraire, les producteurs de messages envoient ceux-ci vers une destination et les consommateurs de messages les récupèrent depuis cette destination.

La destination d’un message est une *queue de message* lors de l’utilisation du domaine *Point-to-Point* alors qu’il s’agit d’un *topic* lorsque l’utilisation d’un domaine *Publish/Subscribe*

Dans ce chapitre, nous allons voir comment utiliser

* les queues de message
* les topics

Les exemples donnés utilisent un contexte Java SE pour simplifier la configuration, reportez-vous à la section *Application Server* pour savoir comment configurer JMS avec un server d’application Wildfly.
