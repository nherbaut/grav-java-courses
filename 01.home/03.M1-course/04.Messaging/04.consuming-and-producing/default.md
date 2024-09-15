# Consommer et produire des messages

Nous avons vu précédemment comment faire transiter des messages d’une source à une destination. Nous allons voir maintenant comment produire et consommer ces messages.

## Produire des messages

Créer une nouvelle classe `PriceProducer` dans le package `fr.pantheonsorbonne.ufr27.miage.camel`.

```java
package fr.pantheonsorbonne.ufr27.miage.camel;

import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.ConnectionFactory;
import jakarta.jms.JMSContext;
import jakarta.jms.Session;
import org.eclipse.microprofile.config.inject.ConfigProperty;


import java.util.Random;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.ScheduledThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

//cette classe est un singleton
//elle implémente l'interface Runnable qui spécifie qu'elle peut être exécutée par un scheduler
@ApplicationScoped
public class PriceProducer implements Runnable {

    //nous récupérons à l'aide de CDI une fabrique de connexions JMS
    @Inject
    ConnectionFactory connectionFactory;

    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;

    //générateur de nombre aléatoire
    private final Random random = new Random();

    //planificateur d'exécution de tache
    private final ScheduledExecutorService scheduler = new ScheduledThreadPoolExecutor(1);

    //cette méthode est appellées lorsque l'initialisation de quarkus est terminée
    void onStart(@Observes StartupEvent ev) {
        //on planifie l'exécution de la méthode run() de cette classe:
        // - immédiatement (initialDelay=0
        // - toute les 5s (period = 5L, unit = secondes)
        scheduler.scheduleAtFixedRate(this, 0L, 5L, TimeUnit.SECONDS);
    }

    //cette méthode est appellées lorsque quarkus s'arrète
    void onStop(@Observes ShutdownEvent ev) {
        scheduler.shutdown();
    }

    //cette méthode est exécutée en boucle par le scheduler
    @Override
    public void run() {
        //syntaxe try-with-resource
        //on crée un nouveau contexte JMS en spécifiant que les sessions sont cloturées automatiquement lors que les messages sont consommés.
        try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
            //on crée un producteur et on y envoie un message dans une nouvelle queue "prices"
            //le message est une chaine de caractères, contenant un entier tiré aléatoirement entre 1 et 100.
            context.createProducer().send(context.createQueue("M1.prices-"+userName), Integer.toString(random.nextInt(100)));
        }
    }
}
```

Cette classe va produire des messages contenant des entiers aléatoires sur une queue de message appelée `M1.prices-` suivie de votre nom d’utilisateur qui sera récupéré depuis le fichier `application.properties` à l’aide de l’annotation `@ConfigProperty(name = "quarkus.artemis.username")`

### Activité

Faites en sorte que les prix soient sauvegardés sur votre disque dur dans le répertoire `data/prices`

 <details>
   <summary>solution</summary>
   <pre>package fr.pantheonsorbonne.ufr27.miage.camel;

import org.apache.camel.builder.RouteBuilder;

import jakarta.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.config.inject.ConfigProperty;

@ApplicationScoped
public class CamelTutorial extends RouteBuilder {


    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;

    @Override
    public void configure() {
        from("sjms2:M1.prices-"+userName).to("file:data/prices");
    }
}</pre>
</details>

## Consommer des messages

Pour consommer des messages, supprimez d’abord toutes les routes de la classe `CamelTutorial`.

Créer la nouvelle class `PriceConsumer` dans le même package que précédemment:

```java
package fr.pantheonsorbonne.ufr27.miage.camel;


import io.quarkus.runtime.ShutdownEvent;
import io.quarkus.runtime.StartupEvent;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.enterprise.event.Observes;
import jakarta.inject.Inject;
import jakarta.jms.*;
import org.eclipse.microprofile.config.inject.ConfigProperty;


@ApplicationScoped
public class PriceConsumer implements Runnable {


    @Inject
    ConnectionFactory connectionFactory;

    @ConfigProperty(name = "quarkus.artemis.username")
    String userName;

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

    @Override
    public void run() {
        while (running) {
            try (JMSContext context = connectionFactory.createContext(Session.AUTO_ACKNOWLEDGE)) {
                //reçoit un message à partir de la queue queue/prices
                Message mess = context.createConsumer(context.createQueue("M1.prices-" + userName)).receive();
                //converti ce message en int
                int price = Integer.parseInt(mess.getBody(String.class));
                //affiche le résultat dans la console
                System.out.println("from the consumer: " + price);

            } catch (JMSException e) {
                e.printStackTrace();
            }
        }
    }
}
```

Puis exécuter votre projet avec votre IDE ou en ligne de commande.

Le consommateur va lire les messages à partir de la même que de message que précédemment, directement fournis par le consommateur.

### Activité

Mettre à jour de consommateur pour lire les messages à partir de la queue `prices-vat` qui va contenir les prix avec la TVA, et mettez à jour la classe `CamelTutorial` pour router tous les messages de la queue `prices` vers une nouvelle queue `prices-vat`.

 <details>
   <summary>solution</summary>
<p>
Dans price consumer:
<pre>Message mess = context.createConsumer(context.createQueue("M1.prices-vat-"+username)).receive();</pre>
</p>
<p>
Dans CamelTutorial
<pre>from("sjms2:M1.prices-"+username).to("sjms2:M1.prices-vat-"+username);</pre>
</p>
