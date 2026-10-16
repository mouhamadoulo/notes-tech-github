En Java, Spring Boot et surtout le framework Spring Framework prennent en charge nativement plusieurs design patterns. L'objectif est de t'éviter de réimplémenter des mécanismes déjà fournis par le framework.
Pour un développeur senior Java / Spring Boot, il est essentiel de savoir reconnaître le pattern utilisé, comprendre comment Spring l'applique et savoir quand l'utiliser explicitement dans son code métier.
Attention à une nuance : certains patterns sont directement implémentés par Spring, d'autres sont facilités par ses annotations et ses mécanismes d'injection, et d'autres restent à ta charge.
1. Les design patterns gérés nativement par Spring
Voici les principaux à connaître pour les entretiens techniques.
2. Tableau des principaux patterns et de leur équivalent Spring
Design pattern	Prise en charge Spring	Mécanisme concret
Singleton	Natif	'@Component, @Service, @Repository
Factory / Factory Method	Natif	BeanFactory, ApplicationContext, @Bean
Dependency Injection (DI) / IoC	Natif	'@Autowired, injection par constructeur
Proxy	Natif	AOP, @Transactional, @Async
Template Method	Natif	JdbcTemplate, RestTemplate, TransactionTemplate
Observer	Natif	ApplicationEventPublisher, @EventListener
Strategy	Facilité	Interfaces, injection de plusieurs implémentations
Repository	Avec Spring Data	JpaRepository, CrudRepository
Adapter	Facilité	Adaptateurs MVC, HandlerAdapter, convertisseurs
Decorator	Facilité	Wrappers, décorateurs, proxies
Builder	Partiel	API de configuration, builders fournis par certaines bibliothèques
Chain of Responsibility	Selon le module	Filtres Servlet, Spring Security, intercepteurs
Front Controller	Natif en MVC	DispatcherServlet
Command	Selon le module	CommandLineRunner, handlers de messages, commandes applicatives
State	À concevoir	Machine à états métier ou Spring Statemachine
Facade	À la charge du développeur	Service applicatif exposant une API simplifiée
Les exemples et les mécanismes ci-dessus s'appuient notamment sur le conteneur IoC, les proxies AOP et les services d'accès aux données de Spring. 


Home
+3



3. Les patterns à comprendre en priorité
1. Singleton : géré par Spring
Par défaut, un bean Spring est partagé au sein de son conteneur : Spring crée une instance pour ce bean et la réutilise lors des injections.
@Service
public class PaymentService {
    public void pay() {
        System.out.println("Paiement effectué");
    }
}


Tu n'as pas besoin d'écrire un singleton avec un constructeur privé et un champ static.
Attention : singleton Spring ne signifie pas nécessairement une instance unique dans toute la JVM. C'est une instance par définition de bean et par conteneur. Il faut aussi éviter de stocker un état mutable propre à une requête dans un service singleton.
2. Factory : gérée par le conteneur IoC
Spring s'occupe de créer les objets et de résoudre leurs dépendances.
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {
        return new PaymentService();
    }
}


Le conteneur gère la création du bean et sa mise à disposition. Tu n'as donc généralement pas besoin d'écrire une factory manuelle pour instancier tes services Spring.
3. Proxy : géré automatiquement pour plusieurs fonctionnalités
C'est un pattern particulièrement important à maîtriser pour un entretien senior.
@Service
public class PaymentService {

    @Transactional
    public void executePayment() {
        // Logique métier
    }
}


Avec la configuration transactionnelle appropriée, Spring utilise un proxy pour intercepter l'appel, ouvrir ou rejoindre une transaction, puis la valider ou l'annuler selon le résultat. 


GitHub
+1



Le même principe d'interception est utilisé par plusieurs fonctionnalités, notamment @Async et @Cacheable, lorsque leurs mécanismes respectifs sont activés.
Point d'entretien : un appel interne comme this.executePayment() ne repasse généralement pas par le proxy. C'est l'une des raisons pour lesquelles @Transactional peut ne pas fonctionner comme prévu lors d'un appel interne.
4. Template Method : fournie par les classes Template
Spring propose des classes qui encapsulent le déroulement technique d'une opération.
@Service
public class PaymentQueryService {

    private final JdbcTemplate jdbcTemplate;

    public PaymentQueryService(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public Integer countPayments() {
        return jdbcTemplate.queryForObject(
            "SELECT COUNT(*) FROM payments",
            Integer.class
        );
    }
}


JdbcTemplate prend en charge les aspects techniques de l'accès JDBC, notamment l'exécution et la gestion des ressources. Tu fournis essentiellement la requête et les éléments propres à ton besoin.
5. Observer : événements applicatifs
Spring permet de publier un événement et de laisser plusieurs composants le traiter sans que l'émetteur connaisse directement tous les destinataires.
public record PaymentCreatedEvent(Long paymentId) {}


Publication :
@Service
public class PaymentService {

    private final ApplicationEventPublisher publisher;

    public PaymentService(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void createPayment(Long id) {
        // Enregistrer le paiement
        publisher.publishEvent(new PaymentCreatedEvent(id));
    }
}


Écoute :
@Component
public class PaymentNotificationListener {

    @EventListener
    public void onPaymentCreated(PaymentCreatedEvent event) {
        System.out.println(event.paymentId());
    }
}


Tu n'as pas besoin de créer toi-même un système de publication et d'abonnement pour ce type d'événements locaux.
Attention : un événement Spring standard n'est pas, à lui seul, un mécanisme de messagerie durable entre plusieurs applications.
6. Strategy : facilitée par l'injection de dépendances
C'est un pattern que tu utiliseras souvent dans du code métier.
Imaginons plusieurs modes de paiement :
public interface PaymentStrategy {
    void pay();
}


@Component("card")
public class CardPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paiement par carte");
    }
}


@Component("transfer")
public class TransferPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Paiement par virement");
    }
}


Spring gère les instances et leur injection, mais c'est toi qui définis les stratégies et la manière de choisir celle qui convient.
Par exemple, tu peux injecter Map<String, PaymentStrategy> et sélectionner une stratégie selon le mode de paiement demandé.
7. Repository : fourni par Spring Data JPA
Avec Spring Data, tu peux déclarer une interface sans écrire toi-même toutes les opérations CRUD.
public interface PaymentRepository
        extends JpaRepository<Payment, Long> {
}


Spring Data fournit une implémentation au runtime pour les opérations héritées, ainsi que des mécanismes de requêtes.
En revanche, les requêtes spécifiques, les règles métier et les choix d'accès aux données restent à ta charge.
4. Ce que Spring ne fait pas à ta place
Même avec Spring Boot, tu dois toujours concevoir les patterns qui répondent à des besoins métier spécifiques.
Besoin	Pattern que tu peux implémenter
Choisir un traitement selon le type de paiement	Strategy
Enchaîner des validations métier configurables	Chain of Responsibility
Simplifier plusieurs services derrière une API métier	Facade
Construire un objet complexe avec des paramètres optionnels	Builder
Gérer les transitions entre états métier	State
Coordonner plusieurs étapes d'un processus métier	Command ou orchestration
Adapter un système externe à ton modèle métier	Adapter
Spring peut fournir des briques qui facilitent certains de ces patterns, mais il ne connaît pas automatiquement tes règles métier.
5. Ce qu'il faut retenir pour un entretien senior
Les six associations à connaître en priorité sont :
1. Singleton → scope par défaut d'un bean Spring.
2. Factory → BeanFactory, ApplicationContext, @Bean.
3. Proxy → @Transactional, @Async, @Cacheable.
4. Template Method → JdbcTemplate, TransactionTemplate.
5. Observer → ApplicationEventPublisher, @EventListener.
6. Strategy → interface et plusieurs implémentations injectées par Spring.
Ajoute à cela Repository avec Spring Data JPA et Front Controller avec DispatcherServlet.
La bonne approche en entretien consiste à expliquer non seulement le nom du pattern, mais aussi le problème qu'il résout, la manière dont Spring le met en œuvre et les limites à connaître.
La règle générale : ne réimplémente pas un pattern déjà fourni par Spring sans raison concrète. Utilise les mécanismes du framework et réserve tes propres patterns aux besoins spécifiques de ton application.
