---

## 1. Jusqu'à quand le compte est-il valable ?

Un compte Oracle Cloud Free Tier se divise en deux phases :

* **Les 30 premiers jours (Essai gratuit) :** Vous recevez **300 $ (environ 250 €)** de crédits valables pendant **30 jours**. Ces crédits vous permettent de tester n'importe quel service payant sur la plateforme. Une fois les 30 jours passés ou le crédit épuisé, ces avantages d'essai expirent.
* **Le statut "Always Free" (Toujours gratuit) :** Il n'a **aucune date d'expiration**. Vos ressources éligibles restent disponibles indéfiniment sans aucuns frais, à condition que votre compte reste actif.

---

## 2. Est-ce que toutes les instances sont gratuites ?

> **Non, absolument pas.** Seules les ressources portant la mention explicite **"Always Free-eligible"** dans la console sont gratuites à vie.

Voici les limites précises sur ce que vous pouvez déployer gratuitement :

### 💻 Serveurs / Serveurs Virtuels (Compute)

* **Instances AMD (x86) :** Jusqu'à 2 micro-serveurs (`VM.Standard.E2.1.Micro`), chacun avec 1/8 d'OCPU et 1 Go de RAM.
* **Instances Ampere (ARM) :** Des machines basées sur l'architecture ARM (`VM.Standard.A1.Flex`). Vous disposez d'un quota cumulé mensuel (jusqu'à 4 OCPUs et 24 Go de RAM à répartir sur 1 à 4 VMs).

### 💾 Stockage & Réseau

* **Stockage bloc (Disques système et données) :** **200 Go au total** cumulés sur l'ensemble de vos serveurs.
* **Stockage Objet (Object Storage) :** 10 Go.
* **Bande passante :** 10 To de trafic sortant par mois.

Toute instance en dehors de ces profils (par exemple une VM Intel plus puissante, des processeurs GPU ou une taille de disque supérieure) est payante.

---

## 3. Risquez-vous de vous faire facturer par surprise ?

### 🔒 En compte Gratuit standard : Risque nul de débit automatique

Lors de l'inscription, Oracle demande une carte bancaire pour vérifier votre identité (une pré-autorisation temporaire de ~1 € peut apparaître puis disparaître).

* Tant que vous n'avez pas manuellement converti votre compte en **Pay-As-You-Go** (Payant à la consommation), **Oracle ne peut pas prélever d'argent sur votre carte**.
* Si vous tentez de créer un serveur payant ou si vos crédits d'essai expirent, le système bloque simplement la création ou stoppe la ressource payante au lieu de vous facturer.

---

### ⚠️ Les seuls pièges à connaître

1. **Le stockage de démarrage (Boot Volume) :**
Lorsque vous créez une instance ARM, Oracle attribue par défaut un disque de 47 Go à 50 Go. Si vous créez trop d'instances ou oubliez de supprimer les disques orphelins (*unattached volumes*), vous pouvez dépasser le plafond global de **200 Go**.
2. **Le nettoyage après les 30 jours d'essai :**
Si vous avez créé des ressources payantes pendant le mois d'essai avec les 300 $ offerts, elles seront arrêtées ou supprimées à la fin des 30 jours à moins de passer en Pay-As-You-Go.
3. **La règle d'inactivité (Reclamation) :**
Si vos instances *Always Free* restent inutilisées pendant une longue période (par exemple, moins de 10 % d'utilisation CPU pendant 7 jours), Oracle peut désactiver la VM pour libérer des ressources. La VM n'est pas facturée, mais elle peut être mise hors tension.

Souhaitez-vous de l'aide pour configurer votre première instance ARM de façon à exploiter au maximum le quota gratuit sans dépasser la limite de stockage ?
