# Comment simuler un flux volumineux de messages sur ma queue

Pour tester efficacement la résilience de votre application (vos threads parallèles et la vitesse d'écriture de votre base PostgreSQL), le but est de remplir la file d'attente d'IBM MQ **très rapidement**, pour observer comment votre Spring Boot local "écope" ce tsunami de messages.

Voici les deux meilleures méthodes pour le faire, selon ce que vous souhaitez tester.

### Méthode 1 : L'injection directe sur le Cloud (La plus violente)

C'est la méthode idéale pour simuler un pic de charge soudain (un "burst"). Au lieu d'envoyer les messages depuis votre PC (ce qui serait ralenti par la latence réseau de votre connexion internet), vous allez générer les messages directement à l'intérieur du conteneur Docker sur Oracle Cloud.

Connectez-vous en SSH à votre serveur Oracle Cloud, puis exécutez cette commande bash. Elle va générer 10 000 messages JSON uniques et les injecter dans la file via l'outil natif d'IBM (`amqsput`) :

```bash
# Remplacez "ID_DU_CONTENEUR" par l'ID ou le nom de votre conteneur MQ (ex: mq-container)
docker exec -i ID_DU_CONTENEUR bash -c 'for i in {1..10000}; do echo "{\"id\": \"TRX_MASS_$i\", \"amount\": $RANDOM}"; done | /opt/mqm/samp/bin/amqsput DEV.QUEUE.1 QM1'

```

**Ce qui va se passer :** IBM MQ va ingérer ces 10 000 messages en quelques secondes. Votre application Spring Boot locale, qui écoute la file, va immédiatement réagir et lancer tous ses threads configurés (votre `concurrency = "5-10"`) pour vider la file et remplir votre base PostgreSQL.

### Méthode 2 : L'API de simulation dans Spring Boot (La plus contrôlable)

Si vous voulez déclencher des tirs de charge à la demande depuis votre IHM Angular ou via Postman, vous pouvez créer un "producteur" directement dans votre code Java en utilisant `JmsTemplate`.

Ajoutez un petit contrôleur REST dédié aux tests de performance :

```java
import org.springframework.jms.core.JmsTemplate;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class LoadTestController {

    private final JmsTemplate jmsTemplate;

    public LoadTestController(JmsTemplate jmsTemplate) {
        this.jmsTemplate = jmsTemplate;
    }

    @PostMapping("/api/test/inject")
    public String injectMessages(@RequestParam int count) {
        long startTime = System.currentTimeMillis();
        
        for (int i = 0; i < count; i++) {
            String jsonMessage = String.format("{\"id\": \"TEST_%d\", \"amount\": 100}", i);
            jmsTemplate.convertAndSend("DEV.QUEUE.1", jsonMessage);
        }
        
        long duration = System.currentTimeMillis() - startTime;
        return count + " messages envoyés en " + duration + " ms.";
    }
}

```

**Comment l'utiliser :** Lancez une requête POST `http://localhost:8080/api/test/inject?count=5000` via Postman.
*Note : Cette méthode est légèrement plus lente que la première car chaque message doit faire le trajet réseau entre votre PC et Oracle Cloud avant de revenir.*

---

> **L'indicateur clé à surveiller : La "Queue Depth" (Profondeur de file)**
> Pendant votre test, ouvrez la console web d'IBM MQ. Observez la jauge **Queue Depth**. Si le chiffre monte en flèche et stagne, cela signifie que votre Spring Boot n'arrive pas à consommer assez vite (souvent à cause de la vitesse d'écriture dans PostgreSQL). S'il redescend rapidement à zéro, votre application est parfaitement dimensionnée !


# Est-ce intéressant de proposer via l’ihm Angular à l’utilisateur de faire un test de chargement d’un nombre de messages (nombre qu’il va définir) dans le cas où quelqu’un d’autre testerait l’application via son docker-compose sur son pc

C'est même une **excellente idée**, surtout pour un projet de type "Proof of Concept" (PoC), un test technique ou un projet d'école !

Ajouter cette fonctionnalité directement dans l'interface Angular transforme votre projet d'une simple application à une véritable **plateforme de démonstration interactive**.

### Pourquoi c'est un énorme plus :

* **Autonomie totale du testeur :** La personne qui lance votre `docker-compose` sur son PC n'aura pas besoin d'installer Postman, de chercher des scripts Bash ou de comprendre comment marche le conteneur IBM MQ. En deux clics depuis le navigateur, elle génère son propre trafic.
* **Preuve concrète de votre code :** Votre cahier des charges impose une contrainte de "résilience et volumétrie". Offrir ce bouton au testeur, c'est lui dire de manière très confiante : *"Testez par vous-même, regardez comme le système encaisse la charge"*.
* **Dynamisme de l'IHM :** Cela rend l'application "vivante". Voir un tableau ou un compteur de messages s'affoler en direct est toujours très satisfaisant lors d'une démonstration technique.

---

### 3 règles d'or pour implémenter cette "Zone de Test"

Si vous partez là-dessus, voici quelques pièges à éviter pour que le testeur ne mette pas son propre ordinateur à genoux :

1. **Mettez un plafond strict (Le plus important) :**
Brider le champ de saisie côté Angular ET côté Spring Boot à un maximum raisonnable (par exemple, 10 000 ou 20 000 messages maximum par tir). Si un testeur curieux tape "10 000 000", il va instantanément faire exploser la RAM de son environnement Docker local (ou saturer votre JVM Spring Boot).
2. **Prévoyez un bouton "Purger / Reset" :**
Si le testeur peut injecter 5 000 messages d'un coup, il doit impérativement pouvoir vider la table PostgreSQL depuis l'IHM (via une route Spring Boot qui fait un `DELETE FROM ...` ou un `TRUNCATE`) pour relancer un test sur une base propre.
3. **Isolez la fonctionnalité visuellement :**
Placez ce formulaire dans un onglet ou un panneau dédié, clairement identifié comme "Outils de développement", "Simulation de charge" ou "Mode Démo". Cela montre que vous avez bien conscience que cette fonctionnalité n'existerait jamais en production.

> **L'astuce UX pour Angular :** Pendant que votre backend Spring Boot boucle pour injecter les messages dans la file, affichez un composant de chargement (un "spinner") sur l'IHM et **désactivez le bouton d'envoi**. Cela évitera que le testeur ne clique frénétiquement 10 fois dessus en pensant que ça n'a pas marché, ce qui lancerait 10 requêtes simultanées.
