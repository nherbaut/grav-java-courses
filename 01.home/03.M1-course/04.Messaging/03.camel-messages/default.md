# Le modèle de messages de Camel

Camel utilise deux abstractions pour modéliser les messages:

`org.apache.camel.Message` : entité fondamentale contenant les données transportées et acheminées dans Camel.
`org.apache.camel.Exchange`: L’abstraction Camel pour un échange de messages. Cet échange de messages comporte un message d’entrée et, en réponse, un message de sortie.

## Message

Les messages sont les entités utilisées par les systèmes pour communiquer entre eux lors de l’utilisation de canaux de messagerie. Les messages circulent dans un sens, d’un expéditeur à un destinataire

![Les messages sont des entités utilisées pour envoyer des données d'un système à un autre.](apache-camel/img/messages.png)

Les messages ont un body (payload, charge utile), des headers (entêtes) et des pièces jointes:

![Un message peut contenir des headers, des pièces jointes et un body.](apache-camel/img/details-d-un-message.png)

Les messages sont identifiés de manière unique avec un identifiant de type java.lang.String. L’unicité de l’identifiant est imposée et garantie par le créateur du message, il dépend du protocole et il n’a pas de format garanti. Pour les protocoles qui ne définissent pas de schéma d’identification de message unique, Camel utilise son propre générateur d’ID.

## Headers et Body

Les headers sont des valeurs associées au message, telles que des identifiants d’expéditeur, des conseils sur l’encodage du contenu, des informations d’authentification, etc. Les entêtes sont des paires nom-valeur ; le nom est une chaine unique, insensible à la casse, et la valeur est de type java.lang.Object. Camel n’impose aucune contrainte sur le type des entêtes. Il n’y a pas non plus de contraintes sur la taille des entêtes ou sur le nombre d’entêtes inclus avec un message. Les entêtes sont stockés sous forme de dictionnaire dans le message. Un message peut également contenir des pièces jointes facultatives, qui sont généralement utilisées pour le service Web et les composants de messagerie.

### Body

Le body est de type java.lang.Object, donc un message peut stocker n’importe quel type de contenu et n’importe quelle taille. Il appartient au concepteur de l’application de s’assurer que le destinataire peut comprendre le contenu du message. Lorsque l’expéditeur et le destinataire utilisent des formats de body différents, Camel fournit des mécanismes pour transformer les données dans un format acceptable, et dans ces cas, la conversion se produit automatiquement avec des convertisseurs de type.

Indicateur de défaut
Les messages ont également un indicateur d’erreur. Quelques protocoles et spécifications, tels que les services Web SOAP, font la distinction entre les messages de sortie et les messages d’erreur. Ce sont tous deux des réponses valides à l’invocation d’une opération, mais cette dernière indique un résultat infructueux. En général, les pannes ne sont pas gérées par l’infrastructure d’intégration. Ils font partie du contrat entre le client et le serveur et sont gérés au niveau de l’application.

## Exchange (Echange)

Lors du routage, les messages sont contenus dans un exchange.

![Un exchange Camel a un ID, un MEP, une exception et des propriétés. Il a également un message d'entrée pour stocker le message entrant et un message de sortie pour stocker la réponse.](apache-camel/img/exchange.png)

* ID d’exchange : un ID unique qui identifie l’exchange. Camel génère automatiquement l’ID unique.
* MEP : pattern qui indique si vous utilisez le style de messagerie InOnly ou InOut. Lorsque le modèle est InOnly, l’exchange contient un message in. Pour InOut, un message de sortie existe également qui contient le message de réponse pour l’appelant.
* Exception : si une erreur se produit à tout moment pendant le routage, une exception sera définie dans le champ d’exception.
* Propriétés : similaires aux headers de message, mais elles durent pendant toute la durée de l’exchange. Les propriétés sont utilisées pour contenir des informations de niveau global, tandis que les headers de message sont spécifiques à un message particulier. Camel lui-même ajoute diverses propriétés à l’exchange pendant le routage. En tant que développeur, vous pouvez stocker et récupérer des propriétés à tout moment pendant la durée de vie d’un exchange.
* Message In : il s’agit du message d’entrée, qui est obligatoire. Le message in contient le message de demande.
* Message Out : il s’agit d’un message facultatif qui n’existe que si le MEP est InOut. Le message de sortie contient le message de réponse.
  L’exchange est le même pendant tout le cycle de vie du routage, mais les messages peuvent changer, par exemple, si les messages sont transformés d’un format à un autre.

Accéder à la JavaDoc de la classe [Exchange](https://www.javadoc.io/doc/org.apache.camel/camel-api/latest/org/apache/camel/Exchange.html)
