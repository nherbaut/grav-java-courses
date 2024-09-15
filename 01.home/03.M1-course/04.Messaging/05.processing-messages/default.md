# Routage avancé et processeur

Jusqu’à présent, nous avons simplement fait transiter des messages d’une queue ou d’un répertoire vers une autre queue, ou un autre répertoire.
Camel nous permet d’appliquer une modification sur les messages à l’intérieur d’une définition d’une route, à l’aide de la classe Processor. Nous allons également voir comment appliquer un traitement différent en fonction du type de message.

## Processor

Nous pouvons définir une classe anonyme implémentant processor pour effectuer le traitement. Tout d’abord, nous allons définir cette nouvelle classe dans `CamelTutorial`.

![création d'un nouveau processor](apache-camel/img/new-processor.png)

Nous pouvons ensuite apporter des modifications au message à l’aide du nouveau processor, pour appliquer une TVA de 20% aux prix. Comme les prix sont maintenant exprimés TTC, ils ne sont plus entiers, donc nous devons changer le type du Body du message:

Dans la class `PriceConsumer`

```java
double price = Double.parseDouble(mess.getBody(String.class));
```

Dans la classe `CamelTutorial`

```java
@Override
    public void configure() {
        from("jms:M1.prices-"+userName).process(new Processor() {
            @Override
            public void process(Exchange exchange) throws Exception {
                int price = Integer.parseInt(exchange.getMessage().getBody(String.class));
                exchange.getMessage().setBody("" + price * 1.20);
            }
        }).to("jms:M1.prices-vat-"+userNAme);
    }
```

## Routage basé sur le contenu

Les taux de TVA n’étant pas toujours les mêmes, notre processeur va devoir appliquer deux taux de TVA différents. Pour ce faire, nous allons devoir appliquer des prédicats sur les messages afin de savoir quel est le bon taux à appliquer. Ces prédicats

### Paramétrage conditionnel des routes

Nous allons ajouter une énumération capable de nous donner le taux de TVA

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

public enum ProductType {
    LUXURY(20),
    BASE(5);

    private final double vatRate;

    ProductType(double vatRate) {
        this.vatRate = vatRate;
    }

    public double getVatRate() {
        return this.vatRate;
    }

}

```

puis nous allons mettre à jour la classe CamelTutorial pour utiliser le taux de TVA pour l’appliquer aux produits.

```java
package fr.pantheonsorbonne.ufr27.miage.camel;


import jakarta.enterprise.context.ApplicationScoped;
import org.apache.camel.Exchange;
import org.apache.camel.Predicate;
import org.apache.camel.Processor;
import org.apache.camel.builder.RouteBuilder;
import org.eclipse.microprofile.config.inject.ConfigProperty;


@ApplicationScoped
public class CamelTutorial extends RouteBuilder {


    @ConfigProperty(name="quarkus.artemis.username")
    String userName;
    @Override
    public void configure() {
        from("sjms2:M1.prices-"+userName).process(new Processor() {
                    @Override
                    public void process(Exchange exchange) throws Exception {
                        ProductType type = ProductType.valueOf(exchange.getMessage().getHeader("productType").toString());
                        double price = Double.parseDouble(exchange.getMessage().getBody(String.class));
                        exchange.getMessage().setBody("" + price * type.getVatRate());
                    }
                })
                .choice()
                .when(new Predicate() {
                    @Override
                    public boolean matches(Exchange exchange) {
                        return ProductType.BASE.equals(exchange.getMessage().getHeader("productType",ProductType.class));
                    }
                })
                .to("sjms2:M1.product-BASE-"+userName)
                .when(new Predicate() {
                    @Override
                    public boolean matches(Exchange exchange) {
                        return ProductType.LUXURY.equals(exchange.getMessage().getHeader("productType",ProductType.class));
                    }
                })
                .to("sjms2:M1.product-LUXURY-"+userName);


    }
}

```

### Activité

Analysez le code pour comprendre ce qu’il fait.

 <details>
   <summary>solution</summary>
<p>
On lit tous les messages de la prices, en leur appliquant le taux de TVA correspondant à leur nature (BASE ou LUXURY) que l'on récupère dans l'énumération ProductType.
Puis on se ressert du header productType pour rediriger les messages vers la queue product-LUXURY ou product-BASE, en fonction de leur type.
</p>

 </details> <details>
   <summary>la classe priceProducer</summary>
<pre>
package fr.pantheonsorbonne.ufr27.miage.camel;
import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.\*;
import org.eclipse.microprofile.config.inject.ConfigProperty;


import java.util.Random;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.ScheduledThreadPoolExecutor;
import java.util.concurrent.TimeUnit;


@ApplicationScoped
public class PriceProducer implements Runnable {

    @Inject
    ConnectionFactory connectionFactory;


    private static final Random random = new Random();

    private final ScheduledExecutorService scheduler = new ScheduledThreadPoolExecutor(1);

    void onStart(@Observes StartupEvent ev) {
        scheduler.scheduleAtFixedRate(this, 0L, 5L, TimeUnit.SECONDS);
    }

    void onStop(@Observes ShutdownEvent ev) {
        scheduler.shutdown();
    }

    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;

    @Override
    public void run() {
        try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
            //on envoie un message de type Text, avec la même payload qu'avant
            Message msg = context.createTextMessage(Integer.toString(random.nextInt(100)));
            try {
                //on rajoute simplement un header indiquant le type du product
                String productType = ProductType.values()[random.nextInt(2) % (ProductType.values().length)].name();
                msg.setStringProperty("productType", productType);
                //et on envoie le prix sur la queue price
                context.createProducer().send(context.createQueue("M1.prices-" + userName), msg);
                System.out.println("message sent of type " + productType);
            } catch (JMSException e) {
                e.printStackTrace();
            }
            ;
        }
    }
}
</pre>
</details> <details>
   <summary>la classe PriceConsumerBase</summary>
<pre>
package fr.pantheonsorbonne.ufr27.miage.camel;
import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.\*;
import org.eclipse.microprofile.config.inject.ConfigProperty;

@ApplicationScoped
public class PriceConsumerBase implements Runnable {
    @Inject
    ConnectionFactory connectionFactory;
    boolean running;

    void onStart(@Observes StartupEvent ev) {
        running = true;
        new Thread(this).start();
    }

    void onStop(@Observes ShutdownEvent ev) {
        running = false;
    }
    @ConfigProperty(name="quarkus.artemis.username")
    String userName;
    @Override
    public void run() {
        while (running) {
            try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
                Message mess = context.createConsumer(context.createQueue("M1.product-BASE-"+userName)).receive();
                double price = Double.parseDouble(mess.getBody(String.class));
                System.out.println("from the consume (base): " + price);

            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
</pre>
</details> <details>
   <summary>la classe PriceConsumerLuxury</summary>
<pre>
package fr.pantheonsorbonne.ufr27.miage.camel;




import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.\*;
import org.eclipse.microprofile.config.inject.ConfigProperty;


@ApplicationScoped
public class PriceConsumerLuxury implements Runnable {


    @Inject
    ConnectionFactory connectionFactory;

    //indique si la classe est configurée pour recevoir les messages en boucle
    boolean running;

    //cette méthode démarre un nouveau thread exécutant l'instance en cours, jusqu'à ce que la variable running soit false.
    void onStart(@Observes StartupEvent ev) {
        running = true;
        new Thread(this).start();
    }


    void onStop(@Observes ShutdownEvent ev) {
        running = false;
    }
    @ConfigProperty(name="quarkus.artemis.username")
    String userName;
    @Override
    public void run() {
        while (running) {
            try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
                //reçoit un message à partir de la queue queue/prices
                Message mess = context.createConsumer(context.createQueue("M1.product-LUXURY-"+userName)).receive();
                //converti ce message en int
                double price = Double.parseDouble(mess.getBody(String.class));
                //affiche le résultat dans la console
                System.out.println("from the consumer (luxury): " + price);

            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
</pre>
</details>

### Utilisation de la DSL Java pour simplifier le routage

La DSL Java de Camel permet d’écrire plus simplement les prédicats basés sur les headers. En transformant le processeur anonyme en classe privée interne, on peut avoir une définition de route beaucoup plus simple.

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import jakarta.enterprise.context.ApplicationScoped;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.builder.RouteBuilder;
import org.eclipse.microprofile.config.inject.ConfigProperty;


@ApplicationScoped
public class CamelTutorial extends RouteBuilder {

    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;

    @Override
    public void configure() {
        from("sjms2:M1.prices-" + userName).process(new VATProcessor())
                .choice()
                .when(header("productType").isEqualTo(ProductType.BASE.name()))
                .to("sjms2:M1.product-BASE-" + userName)
                .when(header("productType").isEqualTo(ProductType.LUXURY.name()))
                .to("sjms2:M1.product-LUXURY" + userName);


    }

    private static class VATProcessor implements Processor {
        @Override
        public void process(Exchange exchange) throws Exception {
            ProductType type = ProductType.valueOf(exchange.getMessage().getHeader("productType").toString());
            double price = Double.parseDouble(exchange.getMessage().getBody(String.class));
            exchange.getMessage().setBody("" + price * type.getVatRate());

        }
    }
}

```

On peut même utiliser directement les headers dans la spécification de la destination, et se passer des prédicats

```java
from("sjms2:M1.prices-"+userName)
        .process(new VATProcessor())
        .toD("sjms2:M1.product-${header.productType}-"+userName);
```

Le pattern implémenté par cette route est le `ComponentBasedRouter` des EIP.

[En savoir plus dans la documentation Apache-Camel sur les expressions simples](https://camel.apache.org/components/4.0.x/languages/simple-language.html)

### Utilisation des logs

Il est possible de logger dans la console les messages transitant par les routes à l’aide de la méthode `.log()`

```java
    from("sjms2:M1.prices-"+userName)
            .process(new VATProcessor())
            .log("recu le prix ${header.productType} + ${body}")
            .toD("sjms2:M1.product-${header.productType}-"+userName);
                
```

### Filtre de message

Il est possible de filtrer les messages en utilisant la DSL Java. Seuls les messages respectant les filtres sont transmis.

Ici, on ne souhaite traiter que les messages correspondant à un produit de type `BASE`

```java
        from("sjms2:M1.prices-" + userName)//
                .filter(header("productType").isEqualTo(ProductType.BASE.name()))//
                .process(new VATProcessor())//
                .toD("sjms2:M1.product-${header.productType}-" + userName);

```

### Multicasting

On peut aussi vouloir dupliquer les messages pour les envoyer dans plusieurs queues simultanément, par exemple sauvegarder tous les messages sur le disque dans le répertoire `accounting` en plus de la queue product-\*

```java
        from("sjms2:M1.prices-"+userName)//
                .process(new VATProcessor())//
                .multicast()
                .toD("sjms2:M1.product-${header.productType}-"+userName)
                .to("file:data/accounting");

```
