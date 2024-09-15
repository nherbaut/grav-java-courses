# La Transformation de message

Camel est capable de transformer le contenu des messages afin de permettre l’intégration de systèmes utilisant des formats différents. Cette transformation peut être aussi simple que d’utiliser un processor, mais d’autres options s’offrent à vous.

## Générer une trace CSV dans un fichier sur le disque

Par exemple, on peut souhaiter transmettre le prix à la queue de message `product-X` tout en générant des traces au format CSV sur le disque. Dans ce cas, un simple processeur peut faire l’affaire:

```java
package fr.pantheonsorbonne.ufr27.miage.camel;
package fr.pantheonsorbonne.artemis;

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
        from("sjms2:M1.prices-" + userName)//
                .filter(header("productType").isEqualTo(ProductType.BASE.name()))//
                .process(new VATProcessor())//
                .multicast()
                .toD("sjms2:M1.product-${header.productType}-" + userName)
                .to("sjms2:M1.accounting-" + userName);


        from("sjms2:M1.accounting-" + userName)
                .process(new CVSPriceProcessor())
                .transform(body().append("\n"))
                .to("file:data/accounting?fileExist=Append&fileName=report.csv");
    }

    private static class VATProcessor implements Processor {
        @Override
        public void process(Exchange exchange) throws Exception {
            ProductType type = ProductType.valueOf(exchange.getMessage().getHeader("productType").toString());
            double price = Double.parseDouble(exchange.getMessage().getBody(String.class));
            exchange.getMessage().setBody("" + price * type.getVatRate());


        }
    }

    private static class CVSPriceProcessor implements Processor {
        @Override
        public void process(Exchange exchange) throws Exception {
            String productType = ProductType.valueOf(exchange.getMessage().getHeader("productType", String.class)).name();
            Double price = Double.parseDouble(exchange.getMessage().getBody(String.class));

            exchange.getMessage().setBody(productType + "," + price);
        }
    }
}
```

### Convertir son objet en bean json avec JaxB

Jusqu’à présent, nous n’avons stocké que des chaines de caractères dans les messages body des messages, mais ceux-ci peuvent également contenir des objets sérialisés au format Json. Pour transformer vos objets en Json, il faut ajouter l’extension `camel-quarkus-jackson` à notre projet.

Définissons un POJO pour contenir nos prix et le type de produit. Créez le record `PriceDTO` dans le même package que précédemment.

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import jakarta.xml.bind.annotation.XmlRootElement;
public record PriceDTO(double price, ProductType productType) {
}

```

Adaptons le producer pour envoyer des objets (class `PriceProducer`)

```java
package fr.pantheonsorbonne.ufr27.miage.camel;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.*;

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
        scheduler.scheduleAtFixedRate(this, 0L, 1L, TimeUnit.SECONDS);
    }

    void onStop(@Observes ShutdownEvent ev) {
        scheduler.shutdown();
    }
    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;
    @Override
    public void run() {
        try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
            //on crée un objet Prix et on l'initialise
            PriceDTO price = new PriceDTO(random.nextInt(100),ProductType.values()[random.nextInt(2) % (ProductType.values().length)]);


            //on envoie un message text contenant du XML
            Message msg = null;
            try {
                msg = context.createTextMessage(toJson(price));
            } catch (JsonProcessingException e) {
                throw new RuntimeException(e);
            }
            context.createProducer().send(context.createQueue("M1.prices-"+userName), msg);
        }
    }

    //cette méthode transforme les PriceDTO en XML (voir la partie de cours sur JaxB)
    private static String toJson(Object obj) throws JsonProcessingException {

            ObjectMapper mapper = new ObjectMapper();
            return mapper.writeValueAsString(obj);

    }
}
```

maintenant, modifions nos routes, pour écrire des objets sur le disque

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import org.apache.Camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.builder.RouteBuilder;

import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class CamelTutorial extends RouteBuilder {

    @Override
    public void configure() {
       from("sjms2:M1.prices-"+userName).to("file:data/product-bean");
    }


}
```

le répertoire `data/product-bean` contient maintenant des fichiers Json.

## Du Json au XML avec Camel

Il est possible de transformer le format de notre objet de Json à XML simplement avec Camel. Pour ce faire, nous avons besoin d’une nouvelle extension `camel-quarkus-jackson` dans notre projet.

Nous pouvons simplement réécrire les routes en indiquant à Camel de:

1. transformer le Json en objet avec jackson (unmarshall)
2. transformer l’objet en XML avec jacksonxml (marshall)

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import jakarta.enterprise.context.ApplicationScoped;
import org.apache.camel.builder.RouteBuilder;
import org.eclipse.microprofile.config.inject.ConfigProperty;


@ApplicationScoped
public class CamelTutorial extends RouteBuilder {

    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;


    @Override
    public void configure() {
        from("sjms2:M1.prices-"+userName)//
                .unmarshal().json()
                .marshal().jacksonXml()
                .to("file:data/product-bean");
    }

}


```
