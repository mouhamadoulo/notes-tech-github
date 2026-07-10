![Image](https://images.openai.com/static-rsc-4/NPsxt_fosSrGB-cDCefkfR1GoQCO7A078GpBjZywv2ZQrjp5KOMwGJVsuUrU8VQwjX-xGaT0yu6flfaIcgUasL-mjAK9ewjFXb18bzHEQ9ApnhJzagiiHQpRmzuj2Y2MQJED28L7v4SShkfPZqzrAEsS5FupIJNlCfHF9mFds9_4JfnNxZUUpXnHcBuP5H1W?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/t-KqbPEqS0R1oEIP8C73lcie23jGVD5MLNDe_ahSN4wSTRQORKRZz3Hbe1z7zH__FS9lTi4aIvf7ehweYmJw0pgcVp0c8Tw7VxO8eOZiS8_PIQuirEk6IHqYkwjJ_zh1o3BUEmrEGvAExtDt8KHxevL41A2WfVjtV9a_HFeYAkxA9H6uaA6d7IlK148SG_Tu?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/kuFzDO1IGDADMsDrmOwKUDimsbdYpCEiUANQUugjvsttTxKkB8uCg7yC82kVtpKxIUDAzSLjVy8rk1m6qkVkQ5P6JYrEZv-fq8SaovqYRtQcqM5aKxTLWBFXPm8UA1a6ceZcyeeOC5zYG0kZ6x9NmYttUt-PtO5ewXmB2hQDlKWM9yRafKGRCoHzVMmkwI8x?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/hpQYJ1Y47PdBcAdiq-NI8zLcKRwwDLjprk_pxAWLtIS376N_WDr_vIlSGb7As46B3a7q7rwcZIaZfEsx4i3kH15rGLukIsol5rW63YL7Lcd0Mp6TToP3hd-0qB_1NHraY3BaIw_VNkM7XSFqIIHxzt3Hrd-YSslJ2VuFrz3WVCoJBOMDN3VHXV21QdU3QfkE?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/C-CcrcETPP-gJHRTJ0dr05-YwXKCdeKRtwCT3LIaU88jdoBB7rkoXxY7_VuTYGipuJYDM2haDIKJc4s_knMhgYIbHKf-mACOnrAEdSQxCaY5BOd_uf4Q5IaTf2IfBwNRR9IJhuBX2ZzA6_tDegMLiNeWcfn9VOZwbkYiJjfwCZtTDVHlzY1f75oZJi0-s1YL?purpose=fullsize)

Coolify est une **plateforme open source de déploiement d'applications** (un **PaaS : Platform as a Service**) qui permet d'héberger et déployer tes applications toi-même, comme une alternative à Vercel, Heroku ou Railway.

L'idée principale : **tu installes Coolify sur ton propre serveur (VPS), et il devient ton mini-cloud personnel.**

### Concrètement, Coolify permet de :

✅ Déployer des applications :

* Next.js
* Angular (avec Nginx)
* React / Vue
* Spring Boot
* Node.js
* Python (Django, FastAPI...)
* Go, PHP, etc.

✅ Gérer automatiquement :

* les builds Docker
* les variables d'environnement
* les domaines
* les certificats SSL HTTPS
* les déploiements Git
* les logs
* les bases de données

✅ Héberger des services :

* PostgreSQL
* MySQL
* MongoDB
* Redis
* MariaDB
* Supabase
* WordPress
* n8n
* Grafana
* MinIO (alternative S3)

---

## Comment ça se compare à Vercel ?

|                     | Coolify           | Vercel                  |
| ------------------- | ----------------- | ----------------------- |
| Type                | Open source       | SaaS propriétaire       |
| Hébergement         | Ton serveur       | Serveurs Vercel         |
| Prix                | VPS (~5-10€/mois) | Gratuit limité / payant |
| Contrôle            | Total             | Limité                  |
| Docker              | Oui               | Partiel                 |
| Next.js             | Oui               | Excellent               |
| Backend Spring Boot | Oui               | Pas idéal               |
| Base de données     | Oui               | Non directement         |
| DevOps              | Plus de contrôle  | Plus simple             |

---

### Exemple avec ton stack Java Angular

Avec Coolify tu pourrais avoir :

```
                 Internet
                    |
              Nom de domaine
                    |
               Coolify
                    |
       -------------------------
       |           |           |
   Angular UI   Spring API   PostgreSQL
       |           |           |
     Docker      Docker      Docker
```

Ton serveur VPS devient une plateforme complète.

---

## Exemple de workflow

Tu pushes ton code :

```
git push origin main
```

Coolify détecte le changement :

```
GitHub/GitLab
       |
       v
   Coolify
       |
       v
 Docker build
       |
       v
 Déploiement automatique
       |
       v
 https://mon-app.com
```

---

## Installation typique

Sur un VPS Ubuntu :

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

Ensuite tu accèdes à :

```
http://IP_DE_TON_SERVEUR:8000
```

Tu configures tes projets depuis une interface web.

---

## Pourquoi beaucoup de développeurs aiment Coolify ?

Parce que ça donne une expérience proche de :

* Vercel pour le frontend
* AWS pour l'infrastructure
* Docker Compose pour les services

mais avec beaucoup moins de configuration.

Pour un développeur fullstack comme toi (Spring Boot + Angular + AWS + Docker), Coolify est intéressant car tu peux créer un **mini environnement DevOps personnel** :

* un VPS OVH/Hetzner
* Coolify
* tes microservices Spring Boot
* PostgreSQL
* Redis
* tes frontends Angular/Next.js
* tes outils IA (n8n, Ollama éventuellement)

---

## Limites à connaître

Coolify n'est pas magique :

❌ Il faut gérer ton serveur :

* mises à jour Ubuntu
* sécurité SSH
* sauvegardes
* monitoring

❌ Moins adapté si tu veux :

* une infrastructure mondiale multi-régions
* du scaling automatique massif
* du serverless pur

Pour une application personnelle, un SaaS ou un projet freelance, par contre, c'est souvent un excellent compromis.

Vu tes projets (Météo Next.js, e-commerce, micro-SaaS MoloPilot, apps Spring Boot), Coolify pourrait être une bonne alternative pour éviter de multiplier les abonnements Vercel/Railway et avoir ton propre environnement de déploiement.
