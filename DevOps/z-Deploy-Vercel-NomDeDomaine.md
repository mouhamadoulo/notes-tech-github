Connecter un nom de domaine personnalisé à une application déployée sur Vercel est une procédure simple qui se réalise en 4 étapes clés.

1. **Acheter le nom de domaine:** Via le registrar de votre choix.
Achetez votre nom de domaine auprès d'un fournisseur (registrar) comme OVH, Cloudflare Registrar, Namecheap, Hostinger ou Gandi.


2. **Ajouter le domaine sur Vercel:**
1. Connectez-vous à votre tableau de bord **Vercel**.
2. Cliquez sur votre projet, puis allez dans **Settings** puis **Domains**.
3. Saisissez votre nom de domaine (ex. `mon-domaine.com` ou `[www.mon-domaine.com](https://www.mon-domaine.com)`) et cliquez sur **Add**.
4. Vercel vous proposera automatiquement d'ajouter la version sans `www` et la version avec `www` (en redirigeant l'une vers l'autre), puis vous affichera les enregistrements DNS nécessaires.


3. **Configurer les enregistrements DNS:** Dans la zone DNS chez votre registrar.
Rendez-vous dans la console de gestion du fournisseur où vous avez acheté le domaine, ouvrez la section **Zone DNS** (ou *DNS Management*) et ajoutez/modifiez les enregistrements requis par Vercel :

* **Pour le domaine racine (`mon-domaine.com`) :**
* **Type :** `A`
* **Hôte / Nom :** `@` (ou laissez vide selon le registrar)
* **Valeur / Cible :** `76.76.21.21`


* **Pour le sous-domaine (`[www.mon-domaine.com](https://www.mon-domaine.com)`) :**
* **Type :** `CNAME`
* **Hôte / Nom :** `www`
* **Valeur / Cible :** `cname.vercel-dns.com`




4. **Attendre la propagation et la génération du certificat SSL:**
Vercel va vérifier régulièrement les enregistrements DNS. Dès que la connexion est validée :

* Le statut sur Vercel passe au vert (**Valid Configuration**).
* Vercel génère automatiquement un certificat SSL (HTTPS) gratuit via Let's Encrypt.

*Note : La propagation DNS prend généralement entre 5 minutes et 1 heure (parfois jusqu'à 24-48 h dans de rares cas).*


---

### Conseil utile

Si vous utilisez **Cloudflare** comme registrar ou proxy DNS, pensez à désactiver le proxy Cloudflare (icône de nuage gris au lieu d'orange) pour les enregistrements `A` et `CNAME` pendant la validation Vercel, afin de laisser Vercel gérer le certificat SSL sans conflit.

---

Voici la procédure étape par étape pour lier votre nom de domaine **OVHcloud** à votre application **Vercel**.


### Étape 1 : Récupérer les informations sur Vercel

1. Allez dans votre dashboard Vercel, sélectionnez votre projet, puis rendez-vous dans **Settings** > **Domains**.
2. Tapez votre nom de domaine (ex: `mon-domaine.com`) et cliquez sur **Add**.
3. Laissez Vercel ajouter la redirection automatique entre la version avec `www` et la version sans `www`.
4. Vercel affiche deux enregistrements DNS à configurer :
* **Un enregistrement A** pour le domaine principal (`76.76.21.21`)
* **Un enregistrement CNAME** pour le sous-domaine `www` (`cname.vercel-dns.com`)



---

### Étape 2 : Configurer la Zone DNS chez OVH

1. Connectez-vous à votre [Espace Client OVHcloud](https://www.ovh.com/manager).
2. Dans le menu de gauche, allez dans **Web Cloud**, puis sous **Noms de domaine**, cliquez sur votre domaine.
3. Cliquez sur l'onglet **Zone DNS**.

#### 2.1 Configurer l'enregistrement A (domaine racine)

1. Cherchez s'il existe déjà une ligne de **Type A** dont la colonne *Sous-domaine* est vide (ou contient juste un `.`).
* **Si elle existe :** Cliquez sur le bouton `...` tout à droite de la ligne > **Modifier l'enregistrement**.
* **Si elle n'existe pas :** Cliquez sur **Ajouter un enregistrement** en haut à droite > Choisissez le type **A**.


2. Laissez le champ **Sous-domaine** vide.
3. Dans **Cible**, saisissez : `76.76.21.21`
4. Validez.

#### 2.2 Configurer l'enregistrement CNAME (pour le www)

1. Cherchez dans la liste si une ligne de **Type CNAME** avec le sous-domaine `www` existe.
* **Si elle existe :** Cliquez sur `...` > **Modifier l'enregistrement**.
* **Si elle n'existe pas :** Cliquez sur **Ajouter un enregistrement** > Choisissez le type **CNAME**.


2. Dans le champ **Sous-domaine**, tapez : `www`
3. Dans **Cible**, saisissez : `cname.vercel-dns.com.` *(N'oubliez pas d'ajouter le point `.` à la fin si l'interface d'OVH le demande)*.
4. Validez.

> **Attention (Suppression des conflits) :** S'il existe d'anciens enregistrements `A` ou `TXT` d'OVH pointant vers une page de parking ou vers un hébergement OVH sur le même sous-domaine (`@` ou `www`), supprimez-les via le menu `...` pour éviter les conflits.

---

### Étape 3 : Vérification et propagation

1. Retournez sur **Vercel** dans l'onglet **Domains**.
2. Cliquez sur le bouton **Refresh** à côté de votre domaine si le statut n'a pas encore changé.
3. Dès que Vercel détecte la mise à jour, un indicateur vert **Valid Configuration** apparaît et le certificat SSL (HTTPS) est généré automatiquement.

*La propagation DNS chez OVH prend généralement entre 15 minutes et 1 heure.*
