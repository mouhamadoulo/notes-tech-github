# Checklist des bonnes pratiques de développement de base de données

Cette checklist couvre les principes essentiels à appliquer lors du développement d'une application utilisant une base de données relationnelle (par exemple : PostgreSQL, MySQL, Microsoft SQL Server).

---

## 1. Conception du modèle de données

### Structure

* [ ] Définir clairement les entités métier et leurs relations.
* [ ] Utiliser des conventions de nommage cohérentes (tables, colonnes, contraintes).
* [ ] Éviter les noms ambigus ou abrégés.
* [ ] Définir des clés primaires pour toutes les tables.
* [ ] Définir les clés étrangères nécessaires.
* [ ] Choisir les types de données les plus adaptés.
* [ ] Limiter l'utilisation des colonnes génériques (JSON, TEXT) aux cas justifiés.
* [ ] Normaliser les données (jusqu'à la 3e forme normale au minimum).
* [ ] Documenter le schéma et les règles métier importantes.

### Intégrité des données

* [ ] Ajouter des contraintes NOT NULL lorsque nécessaire.
* [ ] Utiliser des contraintes UNIQUE pour les données devant être uniques.
* [ ] Définir les contraintes CHECK pertinentes.
* [ ] Configurer correctement les règles de suppression (CASCADE, RESTRICT, SET NULL).
* [ ] Éviter les données dupliquées.

---

## 2. Gestion des migrations

* [ ] Toutes les modifications du schéma sont versionnées.
* [ ] Les migrations sont stockées dans le dépôt de code.
* [ ] Les migrations sont reproductibles sur tous les environnements.
* [ ] Prévoir des scripts de rollback lorsque possible.
* [ ] Tester les migrations avant déploiement.
* [ ] Éviter les modifications manuelles directement en production.
* [ ] Documenter les changements de structure majeurs.

---

## 3. Requêtes SQL

### Performance

* [ ] Sélectionner uniquement les colonnes nécessaires.
* [ ] Éviter les `SELECT *`.
* [ ] Limiter les volumes retournés avec pagination.
* [ ] Utiliser les index appropriés.
* [ ] Vérifier les plans d'exécution des requêtes critiques.
* [ ] Éviter les requêtes N+1.
* [ ] Surveiller les temps de réponse.

### Sécurité

* [ ] Utiliser des requêtes paramétrées.
* [ ] Ne jamais concaténer directement les entrées utilisateur.
* [ ] Valider les données côté application.
* [ ] Éviter l'exécution dynamique de SQL non contrôlé.

---

## 4. Indexation

* [ ] Créer des index sur les colonnes fréquemment filtrées.
* [ ] Indexer les clés étrangères.
* [ ] Vérifier régulièrement l'utilisation des index.
* [ ] Supprimer les index inutiles.
* [ ] Éviter la sur-indexation.
* [ ] Réévaluer les index lors des évolutions fonctionnelles.

---

## 5. Transactions

* [ ] Utiliser des transactions pour les opérations métier atomiques.
* [ ] Garder les transactions aussi courtes que possible.
* [ ] Gérer explicitement les rollback.
* [ ] Définir le niveau d'isolation adapté.
* [ ] Tester les scénarios de concurrence.

---

## 6. Sécurité

### Accès

* [ ] Appliquer le principe du moindre privilège.
* [ ] Utiliser des comptes distincts selon les environnements.
* [ ] Séparer les droits lecture et écriture lorsque pertinent.
* [ ] Désactiver les comptes inutilisés.

### Protection des données

* [ ] Chiffrer les données sensibles.
* [ ] Chiffrer les sauvegardes.
* [ ] Ne jamais stocker les mots de passe en clair.
* [ ] Utiliser des algorithmes de hachage robustes pour les mots de passe.
* [ ] Masquer les données sensibles dans les logs.

### Secrets

* [ ] Stocker les secrets hors du code source.
* [ ] Utiliser un gestionnaire de secrets.
* [ ] Faire tourner régulièrement les identifiants sensibles.

---

## 7. Gestion des performances

* [ ] Mettre en place des métriques de performance.
* [ ] Surveiller CPU, mémoire, I/O et connexions.
* [ ] Détecter les requêtes lentes.
* [ ] Utiliser du cache lorsque pertinent.
* [ ] Effectuer des tests de charge.
* [ ] Définir des objectifs de performance mesurables.

---

## 8. Journalisation et audit

* [ ] Journaliser les erreurs importantes.
* [ ] Tracer les opérations critiques.
* [ ] Conserver un historique des modifications sensibles.
* [ ] Respecter les obligations réglementaires.
* [ ] Définir une politique de rétention des logs.

---

## 9. Sauvegarde et reprise

* [ ] Mettre en place des sauvegardes automatiques.
* [ ] Tester régulièrement la restauration.
* [ ] Définir les objectifs RPO/RTO.
* [ ] Conserver plusieurs générations de sauvegardes.
* [ ] Stocker une copie hors site.
* [ ] Documenter la procédure de reprise.

---

## 10. Environnements

* [ ] Séparer développement, recette et production.
* [ ] Utiliser des configurations distinctes.
* [ ] Anonymiser les données de production utilisées pour les tests.
* [ ] Interdire les accès directs à la production sauf nécessité.
* [ ] Contrôler les changements via un processus de validation.

---

## 11. Qualité logicielle

* [ ] Écrire des tests d'intégration base de données.
* [ ] Tester les contraintes métier.
* [ ] Vérifier les migrations dans la CI/CD.
* [ ] Réaliser des revues de code SQL.
* [ ] Maintenir une documentation technique à jour.
* [ ] Mettre en place des alertes de supervision.

---

## 12. Pour les applications modernes (API, microservices, cloud)

* [ ] Définir clairement le propriétaire de chaque donnée.
* [ ] Éviter les accès directs entre services aux bases des autres services.
* [ ] Utiliser des événements ou API pour les échanges.
* [ ] Prévoir l'évolutivité du schéma.
* [ ] Gérer les montées en charge dès la conception.
* [ ] Anticiper les besoins de partitionnement ou d'archivage.

---

# Vérification avant mise en production

* [ ] Toutes les migrations ont été testées.
* [ ] Les sauvegardes sont opérationnelles.
* [ ] Les index critiques sont présents.
* [ ] Les comptes utilisent le moindre privilège.
* [ ] Les données sensibles sont protégées.
* [ ] Les performances sont validées sous charge.
* [ ] La supervision est configurée.
* [ ] La procédure de restauration a été testée.
* [ ] La documentation est à jour.
* [ ] Les alertes de monitoring sont opérationnelles.

Cette checklist constitue une bonne base pour la plupart des projets professionnels et peut être adaptée selon que vous utilisez une architecture monolithique, microservices, cloud ou on-premise.
