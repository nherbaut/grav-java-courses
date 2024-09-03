# Composants Utiles de Camel

## Direct

[direct](https://camel.apache.org/components/4.0.x/direct-component.html) Le composant Direct permet l’invocation directe et synchrone de tout consommateur lorsqu’un producteur envoie un échange de messages. Ce point d’arrivée peut être utilisé pour connecter des routes existantes dans le même contexte Camel.

```java
from("....").to("direct:toto");
from("direct:toto").to("....");
```

## Bean

[bean](https://camel.apache.org/components/4.0.x/bean-component.html) Permet d’envoyer le message vers un bean pour traitement

```java
package com.foo;

public class MyBean {

    public String saySomething(String input) {
        return "Hello " + input;
    }
}

from("direct:hello")
   .to("bean:com.foo.MyBean");
```

## Rest

* [rest](https://camel.apache.org/components/4.0.x/rest-component.html) permet d’envoyer le message vers une ressource REST.

## PDF

* [pdf](https://camel.apache.org/components/4.0.x/pdf-component.html) permet de créer un PDF avec Camel
