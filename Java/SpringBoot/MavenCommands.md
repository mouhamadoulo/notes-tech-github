# Voici un glossaire des commandes Maven couramment utilisées et leur utilité

1. `mvn clean`: 
Nettoie le répertoire de construction en supprimant le répertoire `target`. 
Cela permet de repartir d'une base propre avant de lancer une construction.

2. `mvn compile`: 
Compile le code source du projet. Par défaut, cette commande compile le code Java du répertoire `src/main/java`.

3. `mvn test`: 
Exécute les tests unitaires en utilisant un framework de test tel que JUnit. Cela compile le code de test et exécute tous les tests.

4. `mvn package`: 
Emballe le projet dans son format distribué, comme un JAR, WAR, ou EAR. 
Cette étape inclut la compilation et l'exécution des tests.

5. `mvn install`: 
Installe le paquet dans le dépôt local, ce qui le rend disponible pour d'autres projets locaux. 
Cela inclut généralement la phase `package`.

6. `mvn deploy`: 
Copie le paquet fini dans un endroit externe pour que d'autres projets puissent l'utiliser, 
habituellement les dépôts distants.

7. `mvn site`: 
Génère un site Maven qui contient des rapports sur la qualité du code, la documentation des API, 
et d'autres informations sur le projet.

8. `mvn verify`: 
Vérifie que le projet est valide et respecte les exigences de qualité telles que la réussite des tests d'intégration.

9. `mvn exec:java`: 
Exécute une classe Java dans le projet en utilisant le classpath du projet. 
Utile pour lancer des applications directement depuis Maven.

10. `mvn dependency:tree`: 
Affiche une arborescence des dépendances du projet. 
Cela aide à comprendre les relations et les conflits possibles entre les dépendances.

11. `mvn help:effective-pom`: 
Montre le POM effectif pour le projet, qui inclut toutes les configurations héritées et configurées par défaut.

12. `mvn help:describe`: 
Fournit une description des phases de cycle de vie de projet et des plugins configurés, 
utile pour comprendre la structure du projet.


Ces commandes couvrent une grande partie des opérations de base que vous pouvez effectuer avec Maven pour gérer vos projets Java. Si vous avez des questions supplémentaires ou des cas d'utilisation spécifiques, n'hésitez pas à demander !